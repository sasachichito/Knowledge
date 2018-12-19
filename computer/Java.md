# データベース接続
【参考】http://www.atmarkit.co.jp/ait/articles/0106/26/news001.html  
## JDBC  
データベースに接続するためのインターフェース（を定めるJavaの標準APIのこと）。  
  
## JDBCドライバマネージャー  
JDBCを実装したクラス。  
プログラマが作成するJavaクラスから利用される。  
JDBCドライバのラッパーになっている。  
  
## JDBCドライバ  
実際にDBに接続するクラス。  
JDBCドライバマネージャーにラップされるため、  
JDBCドライバインターフェースを実装している必要がある。  

# Javaとスレッド  
Javaプログラミングで可能なスレッド操作は、大きく分けて二つある。  
  
- **新しいスレッドで処理させる**  
スレッドの処理内容を定義したインスタンスをnew  
↓  
startメソッドでスレッドを作成・起動。  
※startメソッドをコールするまではスレッドは存在しない。  
- **複数スレッド実行時の挙動を制御する**  
メソッドを実行するであろう複数スレッド群に対して  
あらかじめクラス側で制御処理を書いておく。  

# 例外  
  
## 例外処理とは  
アプリケーションには処理の起点がある。  
Javaであればmainスレッドが実行するmainメソッドか  
mainスレッドから派生したスレッドのrunメソッドのどちらかである。  
https://teratail.com/questions/115164  
これらの起点メソッドが別メソッドを実行し、その先で問題が発生したとする。  
起点メソッドはその問題を検知したい。  
なぜなら起点メソッドが正常終了することはすなわちアプリケーションが正常に動作し終了したということであるから、  
問題が発生したのであれば異常終了しなければならないから。  
そのための仕組みとしてJavaには「例外」がある。  
  
呼び出し元に「例外をスローする」ことで問題が発生したと伝えることが出来る。  
呼び出し元では「例外をキャッチ」して、問題発生時の処理を書かなければいけない。  
  
これを徹底し、起点メソッドが例外をキャッチしなければアプリケーションは正常に終了することになる。  
  
## Javaの例外  
例外には2種類ある  
- 検査例外  
どうしても発生する可能性がある場合にスローする  
- 非検査例外  
適切に扱えば避けられる問題が発生した場合にスローする  
  
検査例外はファイルが見つからない、SQLが実行できないなど、  
アプリケーション側で抑制できない問題が発生したことを呼び出し元に伝える例外である。  
検査例外をスローするメソッドを呼び出す場合、例外をキャッチするか、さらに呼び出し元にスローしなければ  
コンパイルエラーになる。  
  
非検査例外はキャッチしなくてもコンパイルエラーにならない。  
非検査例外がスローされキャッチしていなければ、その時点で異常終了扱いになる。  
  
どちらであっても「例外をキャッチ」した場合、  
握りつぶすか、再スローのどちらかの処理をする。  
- 握りつぶす  
あるメソッドでキャッチして何もしなければ、そのメソッドで例外を握りつぶしたことになる。  
もちろん起点メソッドまで問題発生が伝わることがないので、アプリケーションは正常に動作していると扱われる。  
その例外が発生することも想定内であるときの処理となる。  
- 再スロー  
あるメソッドで例外をキャッチしたとき、呼び出し元に問題発生を伝えるために例外をスローする。  
これを繰り返すと起点メソッドに問題発生が伝わり、アプリケーションが異常であると扱われる。  
  
# equalsメソッドとhashCodeメソッドのオーバーライド  
##### equalsをオーバーライドしていない自作のクラスは、これらのメソッドは何をしている？  
＝＝で比較しているだけ、つまり同じインスタンスじゃないとエラーになる。  
##### hashSetに格納したり、hashMapのキーとして使うならequalsメソッドとともにhashCodeメソッドも実装しなければならない  
##### equalsがtrueを返却する場合、それらhashCodeは必ず同じハッシュ値を返さなければならない(返さないとハッシュアルゴリズムを利用したクラスが正常に動作しない)  
http://education.yachinco.net/tips/java/01/4.html  
##### equalsがfalseを返却する場合、それらhashCodeは異なるハッシュ値を返さなくても構わない(しかしハッシュアルゴリズムを利用したクラスのパフォーマンスが向上しない)  
##### Lombokでハッシュ計算ができる？@EqualsAndHashCodeアノテーションをクラスにつけてあげる  
##### IDEにequalsとhashCodeメソッドの自動生成機能がある  
##### 自前でオーバーライドする場合はObjects.hashメソッドを使う  
https://qiita.com/frost_star/items/8a5d711f9090f0e24f51  
##### samePropertyValuesAsではどんな比較をしている？  
比較対象オブジェクトのプロパティ一覧出してそれぞれを、IsEqualクラスのmatchesメソッドで比較している。  
matchesメソッドはプロパティのequalsメソッドを実行している。  
```  
・比較対象のプロパティの一覧をそれぞれ取得  
・それぞれの一覧の数や名前に差分がないか比較  
・一つづつequalsメソッドで比較  
```  
# クラスの種類  
### トップレベルクラス  
通常のクラス  
### ネストしたクラス  
staticメンバークラス、  
とも呼ばれる。  
トップレベルクラス（外部クラス）のメンバの１つとして定義されているクラスで、static修飾子が付いているもの。  
```  
基本的に「外部クラス名.メンバークラス名」で利用する。  
外部クラスのメンバーにアクセス出来るが、staticなものに限られる。  
```  
外部クラスやそのインスタンスとの関係が比較的薄い。  
  
### インナークラス  
非staticメンバークラス、  
内部クラス、  
とも呼ばれる。  
トップレベルクラス（外部クラス）のメンバの１つとして定義されているクラスで、static修飾子が付いていないもの。  
```  
基本的に「外部クラス名.メンバークラス名」で利用する。  
結び付いている外部インスタンスの非staticメンバーにもアクセス出来る。   
```  
インスタンス化は、外部クラスのインスタンス変数名.new メンバークラスコンストラクタ呼び出し、となる。  
外部クラスのインスタンス(エンクロージングインスタンス)の参照を持つため、バグやGCの思わぬ挙動の原因になりやすい  
  
### ローカルインナークラス、  
ローカル内部クラス、  
ローカルクラス、  
とも呼ばれる。  
インナークラスの一種。  
メソッドやイニシャライザ、コンストラクタで宣言されたクラス。  
クラスの継承やインターフェース実装が可能。  
  
### 匿名インナークラス  
匿名クラス内部クラス、  
匿名クラス、  
無名インナークラス、  
無名内部クラス、  
無名クラス、  
とも呼ばれる。  
インナークラスの一種。  
newと同時にブロックで定義したクラス。  
クラスに名前を持たないのでコンストラクタを定義できない（インスタンスイニシャライザで代替することが多い）。  
  
  
## 日時系
```
人間は１日をだいたい朝・昼・夜に分けて考える。  
「9時は朝」「13時は昼」といった認識を全世界で共有しないと不便である。  
ある瞬間を考えたとき、日本が朝でも、ブラジルでは夜かもしれない。  
つまり同じ共通の時間を利用できない。  
そこで唯一の標準時間(UTC)を定め、各地にはその差分を定義する形にする。（日本の場合は+9時間）  
ある瞬間を考えたとき、当然だがUTCは一意に決まる。  
日本時間はそのUTCに差分の9時間加えると算出できる。  
差分の定義を持つ各地をタイムゾーンという。  
  
unixタイムとはエポック秒とも呼ばれ、UTCの「1970/1/1 00:00:00」からの経過秒数である。  
当然だが、ある日時にはタイムゾーンがどこであろうと必ず唯一のunixタイムがあり、そこからUTCがわかる。  
Javaでunixタイムを表現するのはInstantオブジェクトである。  
System.curremntTimeMillis() は直接unixタイムをlong値で取得できる。  
  
ClockはタイムゾーンかもしくはUTCの指定と、時間の進み方（1分ずつなど）の設定を持つクラスで、日時を表すクラスの生成に利用される。  
Clockは自分のインスタンスが生成された瞬間から日時を保持し、設定通りに日時が進めていく。  
Clockをもとに生成された「日時を表すクラス」は、そのときのClockの日時となる。  
Clockをタイムゾーン/UTCの指定ではなく日時を指定してインスタンス生成すると、日時が進まない。  
  
日時を表すクラスには、Local・Offset・Zonedの3種類が存在する。  
参考：http://www.ne.jp/asahi/hishidama/home/tech/java/datetime.html  
```

## 数値系
```
小数を扱う方法として固定小数点と浮動小数点がある。

固定小数点とは
小数点が特定の位置にある前提で数値を扱う方法。

浮動小数点数とは
仮数部と指数部で数値を扱う方法。

Javaではリテラルの浮動小数点をfloat型とdouble型に格納できる。
floatとdoubleは内部で数値を2進数で管理している。

10進数で記載した小数は、2進数で表しきれないことが多い。（10進数の小数点以下を2進数で表そうとすると、桁の重みの違いからちょうど良く終わらないから。）
結果として最後の方が丸められてしまい誤差が出る。

そこでBigDecimal型が使える。
BigDecimalは内部でも浮動小数点を利用する。そのため小数点以下を2進数で持つ必要がなく、誤差が起きない。
```