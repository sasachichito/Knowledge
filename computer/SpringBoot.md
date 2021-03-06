参考：https://qiita.com/kazuki43zoo/items/8645d9765edd11c6f1dd
https://qiita.com/opengl-8080/items/05d9490d6f0544e2351a
https://xn--fhqq99a3lb.jp/299
https://qiita.com/syukai/items/0d4bf27f82fef9965cdd 
https://qiita.com/gagagaga_dev/items/c16e5b6b3dff6df7e406

# Spring Boot  
Spring Bootは以下のライブラリで構成されるフレームワーク（明確に調べたわけではない）  
```  
・org.springframework.boot:spring-boot  
以下2つのライブラリを使ってspringframeworkを簡単に利用できるライブラリ  
```  
```  
・org.springframework.boot:spring-boot-autoconfigure  
springframeworkの各種プロジェクトを利用するために必要な設定を自動化するためのライブラリ  
```  
```  
・org.springframework.boot:spring-boot-starter  
springframeworkの各種プロジェクトに用意したスターターライブラリの読み込み、依存関係管理を行うライブラリ  
```  
  
### 前提知識  
・Bean定義ファイル = コンフィギュレーションクラス(@Configurationが付与されたクラス)  
・SpringBootが予め用意しているコンフィギュレーションクラスがある。これすなわちAutoConfigure用。  
・AutoConfigure用コンフィギュレーションクラスでは、特定の条件をみたす時（例えば、クラスパス上に指定したクラスが存在する時、指定したクラスのBeanが定義されてない時、など・・）のみ、Bean定義が適用されるようになっている。これにより自分でクラスクラスパス上に配置したjarに含まれるクラスが自動でDIに乗る感覚を得られる。  
・適用条件を表現するためにコンフィギュレーションクラスには@Conditional(条件判定クラス名.class)が付与されている。（もしくはSpringBootから提供される、条件判定クラスまで含めたアノテーション（@ConditionalOnClassなど）が付与されている）  
  
### Bean定義方法  
1. コンフィギュレーションクラス  
2. XML定義  
3. コンポーネントスキャン  
  
どの方法でも@Conditionalを付与できる（はず）。例えば@Profileは内部で@Conditionalを持っているので適用条件を表現できるので、  
コンポーネントスキャンで利用する@Componentと一緒に@Profile("stg")を付与し、stg環境でのみBean定義を有効化できる。  
  
・JdbcTemplateAutoConfigurationのコード抜粋  
```  
@Configuration  
@ConditionalOnClass({ DataSource.class, JdbcTemplate.class }) // (1)  
@ConditionalOnSingleCandidate(DataSource.class) // (2)  
@AutoConfigureAfter(DataSourceAutoConfiguration.class)  
public class JdbcTemplateAutoConfiguration {  
  
    private final DataSource dataSource;  
  
    public JdbcTemplateAutoConfiguration(DataSource dataSource) {  
        this.dataSource = dataSource;  
    }  
  
    @Bean  
    @Primary  
    @ConditionalOnMissingBean(JdbcOperations.class) // (3)  
    public JdbcTemplate jdbcTemplate() {  
        return new JdbcTemplate(this.dataSource);  
    }  
  
    @Bean  
    @Primary  
    @ConditionalOnMissingBean(NamedParameterJdbcOperations.class) // (4)  
    public NamedParameterJdbcTemplate namedParameterJdbcTemplate() {  
        return new NamedParameterJdbcTemplate(this.dataSource);  
    }  
  
}  
```  
```
(1)	JdbcTemplateが格納されているspring-jdbcのjarをクラスパスに含めることで有効となる。
(2)	型によるインジェクションが可能なDataSourceを一つだけ定義することで有効となる。 複数のDataSourceを定義する場合は、JdbcTemplateにインジェクションしたいDataSourceのBean定義に@Primaryを付与すればよい。
(3)	JdbcOperationsの実装クラスDIコンテナにいなければBean定義が行われる。 これは、ユーザ定義のBean定義ファイルでJdbcTemplateのBean定義が行われている場合は、AutoConfigureによるBean定義が無効になることを意味している。
(4)	NamedParameterJdbcOperationsの実装クラスDIコンテナにいなければBean定義が行われる。 これは、ユーザ定義のBean定義ファイルでNamedParameterJdbcTemplateのBean定義が行われている場合は、AutoConfigureによるBean定義が無効になることを意味している。
```

### テスト時にBeanを上書きする  
まずはテスト用実装クラスの@Profile("test")を付与する。  
そしてJVMの起動引数に「-Dspring.profiles.active=test」をつけると本番実装とテスト実装の二つのBeanが有効になる。  
このままではSpringがどっちをDIコンテナに登録すれば良いかわからず「NoUniqueBeanDefinitionException」が出る。  
そのためテスト実装の方に@Primaryをつける。  
これでOK。以下はコンポーネントスキャンで登録されるテスト実装の例。  
```  
@Repository  
@Primary  
@Profile("test")  
public class MyRepositoryTestImple implements MyRepository {  
〜  
}  
```  
コンフィギュレーションクラスを使った場合でも同様にうまくいくと思われる。  
JunitでテストするときはJVMの起動引数の代わりにテストクラスに@ActiveProfiles("test")をつけると楽。

複数のテスト実装Beanを登録しておいて、動的に目的のBeanを取得したい場合は、beanに名前をつけておいて利用する側で@QualifierではなくListやMapで受け取る方法がある。  
しかし本番実装Beanを一つだけDIコンテナから受け取る実装が本番側にある場合、テスト側でListやMapで受け取れたとしても本番側で「NoUniqueBeanDefinitionException」になってしまう。  
複数のテスト実装を動的に切り替えるには工夫が必要。例えばテスト実装Beanがストラテジーとしてテスト実装Mapをコンストラクタで受け取っておき、featureトグルでストラテジーを切り替える方法がある。以下はその例。MyRepositoryのテスト実装を切り替えたい。  
```
// コンフィギュレーションクラス
@Configuration
@Profile("acceptance_test")
public class AcceptanceTestConfig {

    @Bean
    @Primary
    public MyRepository myRepository() {
        Map<String,MyRepository> rmap = new HashMap<>();
        rmap.put("mymy", new MyImpl());
        rmap.put("test", new TestImpl());
        return new RepositoryForAcceptance(rmap);
    }
}

// RepositoryForAcceptanceクラス　テストでDIされるクラス。
public class RepositoryForAcceptance implements MyRepository {

    private Map<String,MyRepository> rmap;
    private String featureToggle;

    public RepositoryForAcceptance(Map<String,MyRepository> rmap) {
        this.rmap = rmap;
    }

    public void setFeatureToggle(String featureToggle) {
        this.featureToggle = featureToggle;
    }

    @Override
    public void done() {
        this.rmap.get(this.featureToggle).done();
    }
}


// テストクラス
@SpringBootTest
@ActiveProfiles("acceptance_test") // コンフィギュレーションクラスを有効化
public class MyTest {

    @Autowired
    MyRepository myRepository; // DI

    @Test
    void test() {
        RepositoryForAcceptance repositoryForAcceptance = (RepositoryForAcceptance) this.myRepository; // フィーチャートグルを設定するためダウンキャスト
        repositoryForAcceptance.setFeatureToggle("mymy"); // フィーチャートグルで設定（切り替え）
        repositoryForAcceptance.done(); // 目的の処理
    }
}
```

### 概念レベルの例外の種類  
```  
ビジネス例外（ビジネスルールに違反した場合）  
正常なライブラリ例外（システムを実現する仕組み上どうしても発生しうる不正な状態。楽観ロックのエラーなど。）  
予期可能なシステム例外（ビジネスルール上あり得えてはいけない状態の発生。事前に存在するはずのデータやファイルがない場合など。）  
予期しないシステム例外（外部APIやDBに接続できない場合など。）  
致命的なエラー（メモリやCPUなどのリソース枯渇によるエラーなど。）  
不正な要求（クライアントのリクエスト方法が不正な場合など。）  
```  
上記の切り分けはエラーを受けたクライアントの大まかな行動指針の違いの観点でもあり、  
それぞれでステークホルダー（利用者・運用者・開発者）が異なる。  
```  
ビジネス例外 : 利用者が再処理する。  
正常なライブラリ例外 : 利用者または運用者が再処理する。  
予期可能なシステム例外 : 利用者または運用者が再処理する。  
予期しないシステム例外 : 運用者または開発者が対応する。  
致命的なエラー : 運用者または開発者が対応する。  
不正な要求 : 利用者が再処理する。  
```  
具体的な再処理・対応内容は、システム検知であればエラーコードで、ユーザー検知であればエラーメッセージで判断する。  
  
### Javaの例外の種類  
Error（致命的なエラーで発生する。OutOfMemoryError、StackOverflowErrorなど。）  
Exception（呼び出し側に問題がない場合でも発生しうるため呼び出し側での補足を強制するために発生する。）  
RuntimeException（呼び出し側に使い方が間違っていることを通知するために発生する。）  
  
### 概念レベルの例外の種類の見分け方  
Javaの例外には3種類しかなく、Errorは「致命的なエラー」を表現する。  
従ってExceptionやRuntimeExceptionの実装クラスを用意して残り5種類を表現する。  
  
### 例外のハンドリングパターン  
1. アプリケーションコードでtry-catchして明示的にクライアントへのレスポンスを生成・返却する  
2. フレームワークにハンドリングさせる  
  
##### ・Spring MVCの例外ハンドリング  
以下の3つのリゾルバが順に例外をハンドリングしていく。  
1. ExceptionHandlerExceptionResolver(@ExceptionHandler)  
2. ResponseStatusExceptionResolver(@ResponseStatus)  
3. DefaultHandlerExceptionResolver(Spring MVCで起きた例外を処理)  
そして最後にErrorControllerを実行する。  
  
ハンドリングパターンとしては以下が一般的。  
1 @Controllerを付与したクラスで@ExceptionHandlerを付与したメソッドを用意する（当該コントローラークラスで指定の例外をハンドリングできる）  
2. @ControllerAdviceを付与したクラスで@ExceptionHandlerを付与したメソッドを用意する（全てのコントローラークラスでの指定の例外をハンドリングできる）  
3. ErrorControllerを実装する（フレームワークにおける全ての例外を捕捉できる）  
他にも独自のAOPを利用した方法などがある。  
  
参考 :   
https://fa11enprince.hatenablog.com/entry/2019/03/06/033248  
http://terasolunaorg.github.io/guideline/5.5.1.RELEASE/ja/ArchitectureInDetail/WebApplicationDetail/ExceptionHandling.html#exception-handling-exception-type-unexpectedexception-label  
https://kiririmode.hatenablog.jp/entry/20140617/p1  
