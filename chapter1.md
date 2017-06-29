## StackStorm で変わる運用
　2016/05 に StackStorm を買収した Brocade Communications Systems, Inc. (以下 "Brocade") は StackStorm を "[ネットワーク・オートメーション・プラットフォーム](http://www.brocade.com/ja/about-us/newsroom/press-releases/2016/may/2016-05-27.html)" と呼んでいます (その後 Brocade を買収した Broadcom Ltd. が、StackStorm を含む [ネットワーク部門を Extreme Networks, Inc. に売却](http://investor.extremenetworks.com/releasedetail.cfm?ReleaseID=1019294))。この他にも "ワークフローエンジン"  や "運用自動化ツール"  などといった呼び方で StackStorm を紹介するブログエントリなどが多数存在します。  
　それぞれが注目する特徴毎にさまざまな呼び方をしていますが、StackStorm CTO の Dmitri Zimine 氏は、StackStorm の本質は "[IFTTT for DevOp](https://www.slideshare.net/brocade/eventdriven-automation-devops-way-iot-73581697)" だと語っています (固有名詞として "IFTTT" というサービスがありますが、ここでは一般名詞としての "IFTTT" を想定しています) 。  
　以下では "IFTTT" と "DevOps" の概念を解説します。これらの理解を通じて、StackStorm の本質を紐解いて行きます。  

### IFTTT について
　"IFTTT (読み：イフト)" という単語は "IF This Then That" の略語で、あるシステムからの入力 (イベント) に対して、全く別のシステムへ出力 (アクション) するという考え方を表します。  

![IFTTT 概要](https://raw.githubusercontent.com/userlocalhost/st2-draft-for-ops/master/images/ifttt.png)

　IFTTT を実現する一例として、図の上段の GitHub / Jenkins による CI(Continuous Integration), CD(Continuous Delivery) がソフトウェア開発者にとって馴染み深いと思います。  
　StackStorm は [StackStorm Exchange](https://exchange.stackstorm.org/) がサポートするさまざまなシステム間で、相互に IFTTT を実現する仕組みを提供します。例えば、仮想基盤のイベントに対して、ロードバランサに対するオペレーションを行ったり、IaaS クラウドにおけるリソースの変更に伴い、Excel を更新するといった処理（またはそれらの組み合わせ）を YAML 形式のファイルで記述することができます。  
　これにより、複数のシステムに跨る複雑なオペレーションを統一的に処理することができるため、多種多用なシステムの管理・運用コストを低減できると見込んでいます。  

### DevOps について
　DevOps は既にバズワードとなって久しく、DevOps については 『[DevOpsを正しく理解しビジネスの価値を高める (中山 貴尋[著])](https://codezine.jp/article/detail/9717)』など多数の解説あります。  
　一般的に "DevOps" とは、何がしかの情報サービスに関わる人間を開発者 (Developer) と運用者 (Operator) に分類し、それぞれの職責を「サービスに対する機能開発によるビジネス価値の向上」と「サービスの継続稼働によるビジネス価値の向上」とした場合、それぞれの仕事がお互いのリスクになる構造に対して、アジャイル開発などの技法や各種ツールを用いて、お互いのリスクを最小化しながら目的 (ビジネス価値の向上) を実現する取り組みを表します。  
　冒頭で示した記事で紹介されている『[DevOps導入指南 Infrastructure as codeでチーム開発・サービス運用を効率化する](http://www.shoeisha.co.jp/book/detail/9784798147604)』でも、DevOps は「Dev(開発) と Ops(運用) が密に協調・連携して、ビジネス価値を高めようとする働き方や文化（"Chapter1-1: DevOps 登場の背景" より抜粋）」と解説されています。  
　注意していただきたいのは "DevOps" が成立する対象は必ずしも "開発者(Dev)" と "運用者(Ops)" だけではないことです。"DevOps" の本質は、それぞれの役割が局所最適化した組織・人同士において、それぞれが連携して全体最適（ビジネス価値の向上）を図ることなので、大局的な利害が一致する組織・人同士であれば、立場の名称に関係なく "DevOps" は成立すると考えられます。  

　ここで StackStorm がどのように『DevOps』を実現するかの一例を簡単にご紹介します。  
　以下の図は何がしかのビジネスを支えるシステム (System) と、システムを管理・運用するチーム (Team)、及びそれぞれのシステムを利用するユーザ (User) を表しています。  

![一般的なシステムの運用](https://raw.githubusercontent.com/userlocalhost/st2-draft-for-ops/master/images/devops1.png)

　ここではユーザが、業務を効率的に行うためにそれぞれのシステムを利用し、各チームはそれらのシステムを維持し、ユーザに提供する役割を負っていると仮定します。  
　チームはユーザに対してシステムの存在を周知し、システムの使い方を示したマニュアルを作成します。  
　しかし、チームによる周知不足のためユーザがシステムについて認知していない場合 (System-A) や、マニュアルが無いなどの理由でシステムの使い方がわからない場合 (System-C) には、ユーザはそれらのシステムを利用できません。  
　またユーザがシステムを認知し、更に使い方を把握していたとしても、複数のシステムを利用する場合、ユーザはそれぞれのシステムの使い方を覚えなければなりません。  

　これに対し StackStorm は、それぞれのシステムを抽象化し、ユーザに対して全てのシステムを統一的に扱えるインターフェイスを提供します。  

![StackStorm を活用したシステムの運用](https://raw.githubusercontent.com/userlocalhost/st2-draft-for-ops/master/images/devops2.png)

　StackStorm によるシステムの抽象化により、システムを運用する各チームがシステムの各機能を StackStorm に登録することで、全てのユーザが全てのシステムの機能を認知・利用できるようになります。  

　システムを利用するユーザにとっては、全てのシステムを統一した方法で横並び (システム毎の使い方の差異をほとんど意識せず) に利用することができるため、より効率的に業務ができると考えられます。  
　またシステムを提供するチームにとっては、システムのオペレーションを StackStorm に統合することによってユーザ周知のための作業コストが減り、またインターフェイスを統一化させることによって使い方を説明するための作業コストも減らすことができます。  

　このように、ユーザとチームの双方が StackStorm を共通認識として持つことで、機能の追加・利用・フィードバックのサイクルをスムーズに循環させ、冒頭で解説した『DevOps』を実現できると考えられます。  

## ここまでのまとめ
　StackStorm CTO の Dmitri Zimine 氏が語った "IFTTT for DevOps" が何を意味するかいついて解説しました。  
　ここまでの内容で、多種多様なシステムを組織的に運用する場面で StackStorm が効果を発揮しそうだということを理解いただけたかと思います。  
　後半では、各社がどのように StackStorm を利用しているかの事例を紹介します。  
