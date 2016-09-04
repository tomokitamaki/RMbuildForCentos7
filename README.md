# 何をするのか
dockerを使用してredmineが動くコンテナを大体20分くらいで自動で作る

# なんで作ったのか
*docker run* するだけでredmineが動くイメージは世の中にたくさんあるけど、  
dockerを触り始めたところなので、自分でも作ってみたいし  
自分で作った経験がいつか生きればいいなと思ったからです。

# このスクリプトを動かすときの準備
- ホストOSにdockerのインストールが終わっていること。
- centos7のdockerイメージを予めpullしておくこと。
- visudoを使用してsudoでdockerコマンドを使用する際にパスワードを求められないようにしておくこと  

# どういう動作をするのか
まず、以下の二つのファイルからなります。  
- buildredmine3.2forcentos7.sh  
シェルスクリプト
- redminebuild_dockerfile  
dockerfile

## 大まかな流れ
1. シェルスクリプトから*docker build* コマンドでコンテナを作る
2. dockerfile の内容に沿ってRUNの行を実行していく
3. dockerfile の内容を完遂したら、シェルスクリプト内の *docker exec* コマンドをどんどん実行する
1. *docker commit* でイメージを作る
1. 作ったイメージを元にして *docker run* する。（ここまで自動）  
1. ブラウザでサーバのIPにポート2222にアクセスする。  
ex)http://192.168.1.1:2222  

# どうして２つのファイルに分かれているのか
シェルスクリプト内で実行しているコマンドは、--privileged オプションと-dオプションをつけて、  
かつ/sbin/initで、 *docker run* したイメージじゃないと *Failed to get D-Bus connection* と  
表示されるerrorになってしまって期待した結果がでません。  
- ex)```sudo docker run -d --privileged IMAGEIDorIMAGENAME /sbin/init```  

dockerfileでそれを実現する方法がわからなかったので  
ホストOSからコンテナ内で実行するコマンドを発行できる方法である  
*docker exec* で代替しました。

