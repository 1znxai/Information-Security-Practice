用Kali Linux學習資安
-密碼竊賊與解密






研究動機
在資訊安全實作2中，已經使用Python練習了基礎的網路漏洞及密碼安全。接下來，繼續深入並且使用資安軟體Kali Linux學習密碼安全 (密碼Cryptography)。由於在上個作品中已有部份的練習，在這份報告中，更像是由基礎到進階的實作。

實作過程（情境模擬）
凱撒密碼：古代的秘密郵件
古羅馬的大將軍凱撒（Julius Caesar），想把作戰計畫偷偷告訴你的副將，但送信的路上可能會被敵人攔截。怎麼辦？凱撒想出了一招：把字母順序「移位元」（shift）！
•	規則：把字母表中的每個字母往後（或往前）移動固定的步數。
•	字母表：A, B, C, D, E, F, G, H, I, J, K, L, M, N, O, P, Q, R, S, T, U, V, W, X, Y, Z。
•	例子：假設我們移位 3 步：
A 變成 D（A + 3）
B 變成 E。
Z 變成 C（回到開頭，像轉圈圈）。
•	加密實戰：
原訊息：HELLO。
移位 3：H → K, E → H, L → O, L → O, O → R。
加密後：KHOOR。
•	解密：知道移位數（鑰匙），就往反方向移回去：
KHOOR → 往前移 3 → HELLO。
凱撒密碼的缺點
太容易被打破了。為什麼？因為只有 26 個字母，最多試 25 次就能破解！於是，聰明的人開始升級密碼學，讓它變得更強大。
比凱撒更厲害：換字母順序（替換密碼）
規則：不只移位元，而是把字母表打亂，比如 A 變成 X，B 變成 K，隨機對應。
例子：訊息 CAT，替換表是 A → X, C → K, T → P，變成 KXP。
難度：試過 25 次不行，因為可能性變成 26!（26 階乘，超大數字）。
還可再升級：多字母換（多表替換密碼）
例子：Enigma 機（二戰用的加密神器），每次按鍵，換字母的方式都會變，像密碼版的萬花筒。
現代密碼學：數學魔法
現在的密碼學不用字母移位元，而是用超級複雜的數學公式。比如 RSA 加密，靠「大質數相乘很簡單，但分解超難」的原理。
還有其他的加解密，我們後面會練習及說明

作業設備架設網路概念及虛擬機（ＶＭ）












Lab實作說明
敵人正在策劃一個大陰謀。他們用鍵盤打密碼（SPY123 和 TOPSECRET），還把電腦鎖起來（SSH user 密碼 123456），甚至把密碼變成亂碼藏在檔案裡（MD5 雜湊）。我們的任務是用『竊賊』技能偷看鍵盤輸入，再用『解密秘技』找出所有秘密
目標：竊取密碼-> 解密碼 -> 攻破系統
用 Pynput 竊取鍵盤輸入，
用 John 破解雜湊，
用 Hydra 攻擊 SSH (port)，
生成報告。





Kali Linux 工具介紹
Kali Linux 是基於 Debian 的 Linux 發行版本，專為網路安全、滲透測試和數位取證設計。它內建許多強大的安全工具，適合資安專家和駭客測試用途。
本次實驗將會用到下列工具
Pynput：偷看鍵盤，像忍者的隱形術。
Pynput 是一個 Python 庫，用於監聽和控制鍵盤與滑鼠，適合自動化操作、鍵盤監聽（Keylogger）、機器人測試等應用。

John the Ripper：破解，像翻開敵人的密碼書。
John the Ripper（JtR）是一款強大的密碼破解工具，主要用於測試密碼強度。它可以通過字典攻擊、暴力破解和混合攻擊來解密各種加密密碼，如 Linux、Windows 雜湊、Wi-Fi 密碼等；
✅ 支持多種加密格式（MD5、SHA、NTLM、ZIP、RAR 等） ✅ 可使用 CPU 或 GPU 進行高速破解 ✅ 支援單一破解模式、字典攻擊、暴力破解
Hydra：攻擊電腦，像用萬能鑰匙開鎖。
Hydra 是一款強大的暴力破解（Brute-force）工具，主要用於測試密碼安全性。它支援多種協定（如 SSH、FTP、HTTP、MySQL 等），能夠自動化密碼猜測，説明安全人員評估系統的弱點。

Shell script

本次實驗將使用shell script呼叫命令，先說明一下
Shell（殼層） 是一種 命令列介面（CLI，Command Line Interface），用來與作業系統溝通。它負責接收使用者輸入的指令，傳給作業系統執行，並回傳結果。Shell有很多種bash, csh等等
Shell Script是一種用來自動化操作 Linux 或 Unix 系統的指令碼語言。它由一系列命令組成，能夠批量執行任務，例如檔案管理、系統設定、網路管理等。

網路探查（network_probe.sh）
目標：探查目標是否存活，測量 ping 時間
實驗內容：發送 10 次 ping 檢查目標 VM (192.168.1.10) 是否存活，記錄時間。
成果：顯示目標的存活狀態與耗時，記錄到 spy_log.txt。

程式碼重點解說
第1行是shell script的要求 (bash shell)
之後的 echo 命令相當於print的意思
第11行對目標電腦執行 ping 命令10次，結果寫入log 檔 (spy_log.txt)
第10/12/13行 : 量測時間
第15/16 : 在Terminal 顯示結果及量測時間

<img width="743" height="456" alt="image" src="https://github.com/user-attachments/assets/6a749ed9-35c1-4cda-b42b-1c87c170bdcd" />

Lab1執行結果
$bash network_probe.sh
鍵盤竊賊(key_spy.sh)
目標：竊取鍵盤輸入並測量時間，記錄按鍵。
實驗內容：輸入 5 個按鍵（例如 spy123 及topsecret），記錄時間與結果
成果：顯示竊取的按鍵，新増(append)記錄到 spy_log.txt
程式碼重點解說
第1行是shell script的要求 (bash shell)
第6行 cat 是指接下的每一行打字(9-27)，要存成 keylogger.py (Python程式碼)；
cat不是” 貓”,是（concatenate）命令 用於 顯示、合併、建立 和 操作文字檔，是 Linux 中最常見的檔處理工具之一
第31行即是跟之前的練習一樣，執行Python程式，只是不用我們在終端機(Terminal)打字，而是shell script會幫我們執行第31行
python3 keylogger.py > key_log.txt  # 模擬輸入 SPY123 或 TOPSECRET
第35行，有個 “| tee”: tee 指令（與管線符號一起使用）可讀取標準輸入， 然後將程式的輸出寫至標準輸出，-a 是特別告知結果要”附加”(append) 在log 檔(spy_log.txt)，因為spy_log.txt在前一個Lab1已經存了Lab1的結果了，整句指令的意思是，用 cat 列印Lab2的結果(已存在stolen_keys.txt)，但因為經由”| tee”， Lab2的結果會由管線接到/附加到log 檔(spy_log.txt)
cat stolen_keys.txt | tee -a $LOG_FILE




注意：需確認目前conda環境有安裝pynput 
$pip install pynput
Lab2執行結果
$bash key_spy.sh
迷宮破解(john_crack.sh)
目標：用 John 破解雜湊並測量時間
實驗內容：破解 spy123 和 topsecret 的 MD5 雜湊
成果：顯示破解結果與時間，記錄到 spy_log.txt
程式碼重點
首先說明MD5: 
MD5 是一種 雜湊函數（Hash Function），用於將輸入資料轉換成固定長度的 128 位元（16 位元組）雜湊值，常用於 密碼存儲、校驗 等應用
生成 MD5 雜湊值的方法
$ echo -n "spy123" | md5sum
輸出結果:  c1c2d2e7e9fbd8e6c4d5e6f7a8b9c0d1
第9/10行，將spy123/topsecret的MD5寫入文字檔 hashes.txt (目標 MD5)



破解詞典: 詞典攻擊是一種 密碼破解技術，透過 嘗試常見密碼列表 來解密 MD5、SHA-1、SHA-256 等雜湊值。比起暴力破解（Brute Force），詞典攻擊更快，因為它使用 預先整理好的常見密碼 來猜測。
詞典攻擊的運作 方式
準備密碼詞典（包含常見密碼，如 123456、password、qwerty）
對詞典內的每個密碼計算 MD5 雜湊值
將計算結果與目標 MD5 值比對
如果匹配，破解成功!!
第17行，可考慮減少詞典字數(只用前1000筆)
第23/24 行，執行”john”進行詞典攻擊


例外處理/容錯 (last but not the least) : 


程式設計要考慮”所有”可能狀況，例如如果使用rockyou.txt 詞典或更小的small_rockyou.txt，無法破解時，要有反應機制，或者改用其他的詞典

Lab3執行結果:
講解:chmod (change mode命令)，把john_crack.sh變成執行檔再於現在目錄(./)下執行，當局也可用shell來執行。
$chmod +x john_crack.sh 
$./john_crack.sh 
依據詞典內有無包括要破解的密碼，有可能會有數種結果，老師修改不同詞典，提供測試
結果:全部成功/部份成功/全部失敗
Lab 3: 迷宮破解 - Thu Mar 6 22:11:00 PST 2025 
目標: 破解密碼雜湊 -------------------------------- 
準備密碼書... 開始破解... 
破解耗時: 3.987 秒 
破解結果: 0/2 個密碼成功 
破解失敗，密碼不在詞典中！ 
0 password hashes cracked, 2 left

<img width="885" height="791" alt="image" src="https://github.com/user-attachments/assets/179b718d-59b5-47c9-8493-46a0b6138349" />

攻擊與鍵盤竊取 (hydra_attack_key_spy.sh)
Lab 3: 迷宮破解 - Thu Mar 6 22:11:00 PST 2025 
目標: 破解密碼雜湊 
-------------------------------- 
準備密碼書... 
開始破解... 
破解耗時: 4.321 秒 
破解結果: 1/2 個密碼成功 
部分破解成功，詞典可能不夠完整。 
spy123:SPY123 
1 password hash cracked, 1 left

目標：破解 VM (192.168.1.10) 的 SSH（user:123456）。
成功後執行「鍵盤竊賊」，限制為 5 秒或 20個按鍵
程式碼重點解說
第14行用hydra命令針對單一密碼解密
如果不成功，改用詞典攻擊
這裡的詞典為Kali Linux內建的rockyou.txt
第24行先檢查rockyou.txt是否在目前執行的目錄之下

<img width="1088" height="426" alt="image" src="https://github.com/user-attachments/assets/a0542f73-0e76-4c21-8249-178d811ba6b0" />

第25行再用解壓縮命令，把rockyou.txt.gz 解壓縮並轉存到 ( > )目前目錄(./)之下的rockyou.txt


<img width="954" height="742" alt="image" src="https://github.com/user-attachments/assets/b7719aeb-9adb-4014-a283-6936fc914d09" />

第29/30行:執行hydra命令(用小型/完整詞典)，開始攻擊網路上有漏洞的電腦(設定的VM)
SSH遠端登入的弱密碼 : user/123456 
顯示耗時及詞典攻擊成功或失敗

若成功破解SSH遠端登入的弱密碼，
第46行，執行ssh命令，遠端登入被破解的帳號

<img width="870" height="450" alt="image" src="https://github.com/user-attachments/assets/0a9bc218-4bc2-4534-82d5-c9fcfdccc419" />

從第47行開始，這些程式或指令【已經是】在遠端電腦執行了
補充講解: 
第47~49 行(安裝系統軟體及pynput)，我們先註解不執行(假設已安裝上述軟體)，因為sudo 是管理員的帳號，目前破解登入的是一般帳號 (帳號名字user)
Linux(MacOS)/Windows作業系統都有管理員的帳號及一般帳號的區別，所以有基本保護，但一旦讓別人可以破解你的系統，危險就變多了。

第67行開始，呼叫pynput的功能開始【竊聽】遠端電腦的Keyboard敲擊(老師配合打字)；主要依據on_press的函式，第63行的設計，竊聽5秒或收集20個字元

顯示遠端竊聽總耗時及結果
Lab 4執行結果






反思與收穫
在這次作品中，直接嘗試使用 Linux 內建的工具讓我對這個系統的了解又更深了，從 pynput 到 John the Ripper，這種辭典式的破解又讓我多學到一種方法，最後使用鍵盤監聽器也讓我更有感覺。
John the Ripper 這種工具讓我知道密碼破解其實有很多方式，而鍵盤監聽 (pynput) 也讓我對輸入監控有了新的認識。這些工具的組合使用，真的能發揮出很多可能性，讓人對資安領域更感興趣。
而且在操作的過程中，還發現一些細節需要注意，比如某些工具需要特定權限才能運作，這讓我更意識到系統權限管理的重要性。另外，對於密碼的安全性，也有了更直觀的理解，像是長度和複雜度真的會影響破解的難度。



參考資料

2022 iThome 鐵人賽做IT必備的資安觀念！手把手帶你攻防實戰
Kali Linux 滲透測試工具｜花小錢做資安，你也是防駭高手, 3/e (碁峰)
Kali Linux 無線網路滲透測試 (博碩文化)
拯救資工系學生的基本素養—SSH & SCP 連線到遠端機器  
