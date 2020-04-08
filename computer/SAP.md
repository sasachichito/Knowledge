# SAP社が提供するERPパッケージ
### 大企業向けERPパッケージのバージョン遍歴  
RF→R/2→R/3→ECC→SoH→S/4 HANA  
  
ECCのサポートが2025年に終了。  
  
### 中小企業向けERPパッケージ  
SAP Business One(B1)... ECCから機能を削ったを提供。  
SAP Business ByDesign... SaaSとして提供。  
  
### SAP NetWeaver  
ミドルウェア。ERPが標準で提供するABAPプログラムや、ユーザが開発したJava・ABAPプログラムの実行環境。  
JavaにおけるWebコンテナ・JREの役割だけでなく、  
DBやリモートサービス接続等を担う多くのコンポーネントで構成されている。  
  
# SAPが提供するERP以外のサービス  
### SAP HANA  
データベース。「S/4 HANA」で採用されている。  
もともとS/4 HANAだけで動くものだったが、クラウド上(AWS,Azure,GCP等)で利用できるDB製品になった。  
  
### CRM
顧客管理ソフト  
  
### BI  
BIソフト  
  
### SaaS  
SuccessFactors... 人材管理  
Concur... 経費精算管理  
等  
  
### PaaS  
SAP Cloud Platform... アプリケーション構築・稼働プラットフォーム  
  
# SAP GUI  
SAPの一般的なユーザインターフェース。3種類ある。  
  
・SAP GUI for Windows  
クライアントアプリ形式。Windowsで動く。  
  
・SAP GUI for Java  
クライアントアプリ形式。Java環境で動く。従ってマルチプラットフォーム。  
  
・SAP GUI for HTML  
ブラウザ(Web GUI)形式。従ってマルチプラットフォーム。  
※ソフト配布の必要性はないが機能に制約あり。  
  
# ABAP  
[参考](https://it-biz.online/sap/abap-abc/)  
  
## 構文  
・TYPEで型を定義  
ABAPディクショナリからデータエレメントとして定義することも可能  
  
・DATAで型の変数を定義  
DATA変数は3種類。  
単一の型の「変数」  
複数の型の「構造」  
複数の構造の「内部テーブル」  
  
・CONSTANTSで型の定数を定義  
  
・FORMでサブルーチン(メソッド)定義  
USING→引数  
CHANGING→戻り値  
  
・PERFORMでサブルーチン(メソッド)呼び出し  
  
・DEFINEでマクロを定義可能  
※ほぼ使われていない  
  
・INCLUDEでプログラムをインクルード  
  
## プログラムタイプ  
ABAPプログラムの作成時に選択する。  
プログラムの種別を表しており、作成するプログラムのソースコードに、対応する宣言命令を挿入する。  
  
・宣言命令  
[参考](https://help.sap.com/doc/saphelp_470/4.7/ja-JP/21/682f8b277e11d2954e0000e8353423/content.htm?no_cache=true)  
  
プログラムタイプ(宣言命令の種別)によって  
プログラムにどの処理ブロックを含めることができるのか、  
ABAP実行時環境によりそのプログラムはどのように処理および実行されるのか、  
独自のDynpro制御ロジックと連動できるのかどうか  
が定義される。  
[参考](https://help.sap.com/doc/saphelp_nw70/7.0.12/ja-JP/fc/eb2d5a358411d1829f0000e829fbfe/content.htm?no_cache=true)  
  
・処理ブロックとイベントキーワード  
処理ブロックはABAPプログラムのソースコードの一部を指す。  
ABAP実行環境はユーザーの操作に対応するイベントを発行し、対応する処理ブロックの実行を行う。  
対応する処理ブロックはイベントキーワードによって決定する。  
[参考](https://help.sap.com/doc/saphelp_46c/4.6C/ja-JP/9f/db9a0735c111d1829f0000e829fbfe/content.htm?no_cache=true)  
  
・プログラムタイプ一覧  
TYPE 1: 実行可能プログラム  
TYPE M: モジュールプール  
TYPE F: 汎用グループ  
TYPE K: クラスプール  
TYPE J: インタフェースプール  
TYPE S: サブルーチンプール  
TYPE T: タイププール  
TYPE I: インクルードプログラム  
[参考](https://help.sap.com/doc/saphelp_470/4.7/ja-JP/e4/2adbd7449911d1949c0000e8353423/content.htm?no_cache=true)  
TYPE1、Mのみ単体で実行可能。  
[ABAPプログラムからTYPE1、Mの呼び出し方](https://help.sap.com/doc/saphelp_470/4.7/ja-JP/9f/db9d7535c111d1829f0000e829fbfe/content.htm?no_cache=true)  
  
・TYPE 1: 実行可能プログラム  
レポートプログラムと呼ばれる。  
プログラム名の入力、トランザクションコードの使用で実行可能。  
ABAP実行環境は固定された順序でイベントを発行し、一連の処理ブロックを実行。  
  
・TYPE M: モジュールプール  
ダイアログプログラムあるいはDynproと呼ばれる。  
トランザクションコードを使用してのみ開始可能。画面が起動する。  
画面自体とその(Dynpro)制御ロジックで構成され、制御ロジックは複数のPBO/PAIダイアログモジュールで構成される。  
ABAP実行環境は(Dynpro)制御ロジックに従ってイベント発行、対応する処理ブロックを実行する。  
[参考](https://help.sap.com/doc/saphelp_470/4.7/ja-JP/9f/db9cdc35c111d1829f0000e829fbfe/content.htm?no_cache=true)  
  
・TYPE F: 汎用グループ  
汎用モジュールのコンテナプログラム。  
  
・TYPE K: クラスプール  
グローバルクラスのコンテナプログラム。  
  
・TYPE J: インタフェースプール  
グローバルインターフェースのコンテナプログラム。  
  
・TYPE S: サブルーチンプール  
サブルーチンのコンテナプログラム。  
  
・TYPE T: タイププール  
タイプ(型)のコンテナプログラム。  
  
・TYPE I: インクルードプログラム  
他のプログラムタイプのプログラムからインクルードされて利用できるプログラム。  
  
## クラス  
ローカルクラスとグローバルクラスがある。  
  
・ローカルクラス  
プログラムの中で定義されたクラス  
そのプログラム内でのみ利用可能。  
  
・グローバルクラス  
クラスビルダ(Tr-cd:SE24)で定義されたクラス。  
(プログラムタイプがクラスプール)  
全てのプログラムから利用可能。  
  
## 汎用モジュール  
汎用グループ→staticクラス  
汎用モジュール→staticメソッド  
  
IMPORTING→引数  
EXPORTING→戻り値  
  
Tr-cd:SE37 汎用モジュールエディタで定義(F8押下でテストも可能)  
(プログラムタイプが汎用グループ)  
  
CALL FUNCTIONで汎用モジュール呼び出し  
  
## リモートAPI  
ABAPプログラムをリモートプログラムから呼び出すインターフェース。  
  
### RFC  
リモートファンクションコールの略。  
リモートシステムの汎用モジュールを呼び出すプロトコル。3種類ある。  
・sRFC  
・tRFC  
・qRFC  
[参考](https://help.sap.com/doc/saphelp_nw70/7.0.12/ja-JP/6f/1bd5b6a85b11d6b28500508b5d5211/content.htm?no_cache=true)  
tRFCはトランザクション処理のための特性(同時実行制御とACID)に対応している(?)  
  
RFC汎用モジュール(RFCで呼び出される汎用モジュール)を作成するには  
通常の汎用モジュール作成手順の中で明示的に指定する。  
  
プログラムからRFC汎用モジュールを呼び出すには  
CALL FUNCTIONにDESTINATION句をつけて、システムに登録済みのリモートホストを指定する。  
[参考](https://help.sap.com/doc/saphelp_470/4.7/ja-JP/22/04250b488911d189490000e829fbbd/content.htm?no_cache=true)  
  
RFC汎用モジュールは同一SAPのABAPプログラムから利用することも可能。  
  
・BAPI  
SAPに標準で用意されているRFC対応汎用モジュール。  
SAPの標準機能(実態はABAPプログラム)のAPIとして動作する。  
  
### バッチインプット  
大容量のデータセットを SAP システムに転送する際に使用するプロトコル。  
  
### GUI  
SAP GUIとSAP間のプロトコルを外部プログラムで実装する方式。  
  
## ABAPディクショナリ（データディクショナリ/ディクショナリ/DDIC）  
[参考](https://help.sap.com/saphelp_tm92/helpdata/en/cf/21ea0b446011d189700000e8322d00/frameset.htm)  
ABAPディクショナリ、データディクショナリ、ディクショナリ、DDICは同義。DDICはdata dictionaryの略。  
大きく3種類の機能を持つ。  
・データベースの管理  
・型の管理  
・その他の管理  
  
### データベースの管理  
ABAPディクショナリはSAPシステムの中央データベースのテーブルを管理する。  
ディクショナリにはテーブルのメタ記述が含まれ、これらを使用して中央データベースを管理する。  
  
ビューやテーブルを持ち、カラムには型(エレメント)を割り当てる必要がある。  
カラムはテーブルのフィールドとも呼ばれる。  
  
各テーブルは各オブジェクトモデル(DDDにおける集約)に対応し、実際にデータが格納される。  
例）顧客テーブル  
テーブルを型としてABAPプログラムから利用できる。  
  
またロック制御のためにロックオブジェクトを定義することもある。  
  
### 型の管理  
エレメント、構造、テーブルの3種類の型を定義できる。  
※ここでのテーブルはデータベース管理のテーブルとは意味が異なるため注意。  
エレメントはデータベース管理におけるテーブルのフィールドとして利用される。  
  
エレメントにはドメインとよばれる不変条件を紐づけることができる。  
これらの型はABAPプログラムから利用できる。  
  
エレメントは基本型か参照型にすることができる。  
・基本型  
ABAPディクショナリで事前定義しているデータ型か、  
それにドメインを紐づけたエレメント。  
  
・参照型  
グローバルなクラス・インターフェースの参照を持てるエレメント。  
  
# SAP Fiori  
SAP GUIに代わるUI。  
[参考](https://www.sapjp.com/blog/archives/12657)  
  
# OData  
HTTPの設計原則(RESTに近い)  
[参考](https://qiita.com/tami/items/411a226d1ea6bb25b5f1)  
  
SAP Netweaver GatewayサーバでODataリクエストを受け付けて、SAPにRFCでバックポスト可能。  
[参考](https://qiita.com/tami/items/a87a6f67be928c78e4e8)  
  
# 調査中  
OLE  
EDI  
IDoc  