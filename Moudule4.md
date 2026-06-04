用Kali Linux學習資安
- Metasploit 滲透測試






研究動機
在資訊安全實作 1/2 中，我透過 Python 練習了基礎的 網路漏洞測試 和 密碼安全，了解了基本的漏洞分析與攻擊手法。
在自主學習 8，我使用 John the Ripper、Hydra 和 Pynput 進一步學習了 密碼攻擊與解密技術，並探索了各種破解與防禦策略。
因此，在自主學習 9，我將使用 Metasploit 進行實際的 滲透測試挑戰，專注於 攻擊四台 SSH 伺服器。
本次學習的目標是模擬真實的滲透過程，理解 SSH 服務的安全風險，並嘗試不同的攻擊手法，以提升對滲透測試與資安防禦的理解。





Metasploit：忍者的秘密武器庫
Metasploit 是一個 強大的資安工具，主要用來進行 滲透測試（Penetration Testing）。它可以模擬駭客攻擊，幫助資安人員找出系統漏洞，進而加強防禦。

為什麼要學 Metasploit？
學習駭客如何攻擊，提升資安防禦能力
可以模擬真實的網路攻擊，但要合法使用！
容易上手，內建許多攻擊模組
Metasploit 主要功能
掃描漏洞 🕵：找出系統有哪些安全問題
利用漏洞 🔓：嘗試進行攻擊（例如取得 SSH 存取權限）
滲透攻擊 🎭：成功入侵後，可以遠端控制目標系統





作業設備架設網路概念及虛擬機（ＶＭ）
ＶＭ架構與之前自主學習6/7/8一致 
但192.168.1.11/.15/.16 皆新增(老師操作)
user/123456
啟用SSH
$ sudo apt install openssh-server -y 
$ sudo systemctl enable ssh 
$ sudo systemctl start ssh 
$ sudo adduser user # 密碼設為 123456

 








Lab實作說明
敵人把四台電腦（.10, .11, .15, .16）藏在迷霧中，裡面裝滿機密檔案。他們用弱密碼（user:123456）保護這些電腦，現在我們的任務是用 Metasploit 攻進去，點亮我們的「戰鬥地圖」，看看能抓到多少秘密
目標：掃描敵人基地->偷取敵人資訊->攻擊與戰鬥報告！
使用Metasploit它是駭客界的「瑞士軍刀」，2003 年由 H.D. Moore 打造，現在全球【忍者】都在用！它能：
像雷達：掃描敵人電腦，找到沒鎖的窗戶（漏洞）。
像飛鏢：射出攻擊，直接進去偷秘密。
像忍術書：裡面裝滿招式，從偷密碼到控制電腦，超強！
今天，我要用它攻擊四個敵人（.10, .11, .15, .16），畫出戰鬥地圖，還要點亮勝利燈號，像打遊戲破關一樣

網路探查（ninja_scan.sh）
目標：用 Metasploit 掃描四個目標(掃描敵人基地)，畫出簡單地圖
程式碼重點解說
第4-7行是先取得自已的IP Address，用的命令( ip adder show grep awd…)都是Linux內建命令;eth0是指硬體網路卡(有線)
第11-18就是產生Metasploit的命令掃瞄
192.168.1.10 ~ 16
TCP/PORT 22 : TCP網路協議22埠 (port如同港口)
THREADS 4: 英翻中是線程，也就是4個平行處理的動作 
第21行執行Metasploit 掃瞄命令
第23行後是產生報告，之後Python程式碼，生成掃瞄地圖 (Python程式碼細節省略)
<img width="1049" height="824" alt="image" src="https://github.com/user-attachments/assets/fe0612e8-9bec-442b-854f-45b98b9d1fbf" />

Lab1執行結果
$bash ninja_scan.sh
scan_log.txt：只是顯示有哪些目標(可探測到連線)，但不表示目標VM都有SSH 開啟
ninja_map.png：簡單地圖，藍色節點，從.20 (我的VM) 掃瞄到四個可能的目標VM (.10, .11, .15, .16)
<img width="1026" height="684" alt="image" src="https://github.com/user-attachments/assets/7d54c152-88f0-4f3a-9d64-cce5fc326a54" />


忍者情報站(ninja_info.sh)
目標：用 Metasploit 收集目標的系統資訊，生成情報柱狀圖，即找出有開啟SSH的目標VM，為最理想的敵人弱點/漏洞!
程式碼重點解說
與Lab 4-1類似但不同之處，是這裡用Metasploit找尋漏洞(例如SSH): 參數不同
use auxiliary/scanner/ssh/ssh_version
<img width="919" height="788" alt="image" src="https://github.com/user-attachments/assets/01a09812-bbf2-4d4f-a6f8-cf602d651746" />

注意：需確認目前conda環境有安裝以下套件 
$ sudo apt install metasploit-framework 
$ pip3 install matplotlib networkx pillow

Lab2執行結果
$bash ninja_info.sh

ninja_info.png：橙色柱狀圖，顯示哪些目標有 SSH 版本資訊（例如 3 根柱子高，1 根低）。
因為目標VM版本一致，所以柱狀圖高度會一樣
不過，如何不同的VM上的密碼較弱，即便使用的SSH版本一樣，也是更容易被攻破的
 
 

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/17656940-5374-4d8c-ac5a-c4f07fdb2edc" />



4.3 攻擊與戰鬥報告(ninja_attack.sh)
目標：得到情報之後，用 Metasploit 攻擊目標的可能漏洞(SSH)，生成動畫地圖與燈號圖
程式碼重點解說
第10-19行，生成Metasploit 命令
第22行，這裡直接用Metasploit 攻擊目標VM，找尋漏洞，由其SSH登入目標電腦
第37行之後，使用Python從報告(…log.txt)讀取資訊，產生圖檔。(此處細節省略…) 

<img width="1009" height="820" alt="image" src="https://github.com/user-attachments/assets/fa4bb0fb-eaf9-4bac-8bb4-6c0da6b26432" />

注意：需確認目前conda環境有安裝以下套件 
$ sudo apt install metasploit-framework 
$ pip3 install matplotlib networkx pillow

Lab3執行結果
忍者戰鬥報告 成功攻破基地: 3/4
$bash ninja_attack.sh
ninja_attack.gif：攻擊方(我.20藍色).10, .11, .15目標VM灰色
<img width="798" height="532" alt="image" src="https://github.com/user-attachments/assets/2fad1cdc-5f57-4122-a757-29dfb228d48f" />

ninja_lights.png：3個灰點是表示被我們攻擊成功的目標VM ，表示我們的程式正確執行。
<img width="870" height="434" alt="image" src="https://github.com/user-attachments/assets/cc92aa52-0c1d-4594-b983-96c98314eafa" />

5.反思與收穫
在自主學習 6/7，我用 Python 進行資安實驗，自己寫程式來破解密碼、測試漏洞，雖然能掌握攻擊的基礎邏輯，但過程比較土法煉鋼，像是暴力破解時，要自己寫腳本載入字典、測試密碼，甚至處理網路請求，整體來說雖然有收穫，但效率不高。  
到了自主學習 8/9，我開始使用 Kali Linux 內建的工具，像 John the Ripper、Hydra 和 Metasploit，再搭配 Python 腳本，能更快速完成各種攻擊測試，整個流程順暢很多。其中 Metasploit 讓我真正體驗到滲透測試的完整過程，從掃描漏洞、攻擊目標到後滲透控制，這些高度自動化的工具讓我對駭客攻擊與企業防禦的運作方式有更直觀的理解。  
透過這兩種方式的對比，我發現 Python 雖然強大，但專業資安工具可以大幅提升效率。如果能結合兩者，應該能讓攻擊與防禦的學習更深入，未來也想試試更進階的滲透測試技術！

6.參考資料

2022 iThome 鐵人賽做IT必備的資安觀念！手把手帶你攻防實戰
Kali Linux 滲透測試工具｜花小錢做資安，你也是防駭高手, 3/e (碁峰)
Kali Linux 無線網路滲透測試 (博碩文化)
拯救資工系學生的基本素養—SSH & SCP 連線到遠端機器  
