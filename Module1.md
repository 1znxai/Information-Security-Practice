用Python學習網路資訊安全 
網路地圖分析，密碼破解，漏洞偵測及密碼破解






研究動機
隨著科技的發展，網路已成為我們日常生活的一部分，但同時，資安威脅也越來越嚴重。例如，個人資料外洩、網路詐騙、駭客攻擊等問題，可能影響個人隱私和社會安全。因此，我希望透過自主學習，更深入了解資訊安全的基本概念、常見攻擊方式及防禦方法，提高自身的資安意識，也能與身邊
  的人分享這些知識，降低資安風險。
此外，許多企業和政府機構都非常重視資訊安全，這讓我對資安領域的發展充滿興趣，希望透過學習，探索未來可能的職業方向。這次計畫將透過閱讀相關書籍、參與線上課程及實作練習，培養基礎資安能力，為未來學習更進階的資安技術打下基礎。




作業設備架設網路概念及虛擬機（ＶＭ）
網路資訊安全的實驗設備，一樣可以先問ChatGPT注意事項及建議架設方式

安全性
進行資安實驗時，務必使用獨立的網路環境（如本地 虛擬機VM 或封閉網段），避免影響學校或家中網路。
測試時避免攻擊公共網路或未授權系統，以免違法。
方法
低成本方案
使用 免費的虛擬機 + 開源工具（Kali Linux、Wireshark．．）




綜合以上
使用 筆電x2 + 數個虛擬機（VM）
虛擬機軟體使用VirtualBox
虛擬機軟體上跑Kali Linux(一種Linux作業系統，專門用來資安的實驗用的)
路由器(router) x1，只使用封閉區域網路，不上網際網路

架構示意圖如下
另一台電腦CPU有8核心,記憶體有16GB，執行四個虛擬機，以有線網路連到路由器
我的電腦只執行一個虛擬機，以無線網路連到同一個路由器
路由器不連上網際網路，形成一個封閉的測試網路


每個虛擬機是一台Kali Linux的機器，分別有自己的網路地址 (192.168.1.x)
通常電腦上網會是用被動態分配網址(DHCP)
動態主機設定協定（英語：Dynamic Host Configuration Protocol，縮寫：DHCP）
本實驗為了方便驗證及觀察，192.168.1.x 是由老另一台電腦在每個虛擬機設定的不同的靜態(Static)網址
每個電腦的網址一定要不同，以免衝突，如同每人的家的住址都不同。


















小實驗1：如何確認自已電腦（虛擬機）的網路地址

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::a00:27ff:fe81:d6da  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:81:d6:da  txqueuelen 1000  (Ethernet)
開啟Kali Linux之後，在終端機裡打下列指令，在inet後的即為目前的網路地址
$ ifconfig
小實驗2：如何確認電腦的網路連接正常

由於我們知道其他電腦的網址
$ ping 192.168.1.20開啟Kali Linux之後，在另一台終端機裡打下列指令 ping我的電腦

└─$ ping 192.168.1.20
PING 192.168.1.20 (192.168.1.20) 56(84) bytes of data.
64 bytes from 192.168.1.20: icmp_seq=1 ttl=64 time=6.12 ms
64 bytes from 192.168.1.20: icmp_seq=2 ttl=64 time=4.49 ms
64 bytes from 192.168.1.20: icmp_seq=3 ttl=64 time=4.02 ms
64 bytes from 192.168.1.20: icmp_seq=4 ttl=64 time=3.22 ms
64 bytes from 192.168.1.20: icmp_seq=5 ttl=64 time=2.58 ms正常連接的訊息像下列，表示另一台電腦的Kali Linux 虛擬機跟我們是連正常，會在一定的時間內回應 (2~6 ms millisecond )資安實驗
網路地圖探險家：掃描端口，生成彩色地圖
目標：掃描網路並生成動態設備地圖，模擬資安偵察並標示潛在目標
詳細步驟：
在我的電腦VM 上執行程式碼。
$ python 1network_explorer.py。
掃描網路，顯示活躍 IP 並檢查 SSH 端口（模擬漏洞）。
跳出彩色地圖：紅點表示 SSH 開放（潛在弱點），綠點表示無開放端口。
猜哪些 VM 是「易攻擊目標」?



<img width="634" height="516" alt="image" src="https://github.com/user-attachments/assets/22b7d0fb-c62d-4062-8022-5455249e4039" />

















本節心得: 根據設定，有一台Kali Linux電腦設定SSH為開啟，我在自已的Kali Linux虛擬機執行的程式，確實可以找到另一台電腦有開啟SSH。

安全殼層協定（Secure Shell Protocol，簡稱SSH）是一種加密的網路傳輸協定，可在不安全的網路中為網路服務提供安全的傳輸環境[1]。SSH通過在網路中建立安全隧道來實現SSH客戶端與伺服器之間的連接[2]。SSH最常見的用途是遠端登入系統，人們通常利用SSH來傳輸命令列介面和遠端執行命令。SSH使用頻率最高的場合是類Unix系統，但是Windows作業系統也能有限度地使用SSH，但是SSH也被指出有被嗅探甚至解密的漏洞，在後面實作中，我們將試著透過SSH，破解遠端電腦使用”弱密碼”的使用者
	
密碼破解戰士：模擬暴力破解，生成強密碼
目標：模擬暴力破解並生成強密碼建議。
加入簡單破解測試，提升資安意識。
問：「駭客怎麼猜你的密碼？你能防住嗎？」
比喻：像試開一把數字鎖。
詳細步驟：
在我的電腦Kali Linux VM 上執行程式碼。
$ python 2password_warrior.py。

<img width="1151" height="822" alt="image" src="https://github.com/user-attachments/assets/aab51515-7271-41b7-94a0-f2701b38f87f" />


試驗結果(如下圖)

<img width="540" height="276" alt="image" src="https://github.com/user-attachments/assets/7fdb40b4-3dd6-4dd3-b23d-6324860945df" />


<img width="538" height="248" alt="image" src="https://github.com/user-attachments/assets/c19cd56d-6267-4dc6-ad03-9d3b753c4b97" />

<img width="288" height="300" alt="image" src="https://github.com/user-attachments/assets/ae02b087-30c8-4094-8648-9aea707b17f0" />


本節心得: 簡單的密碼(123456)很快被破解；更複雜的密碼可以延長被破解時間；如果駭客使用更快的電腦或其他新的方法，密碼被破解時間愈快






漏洞獵人：掃描漏洞，生成攻擊報告

目標：模擬資安專家，使用 Python 掃描網路中的漏洞（例如開放端口與弱密碼），生成攻擊性報告，並展示簡單的 SSH 登入嘗試。
難度提升：
結合端口掃描與弱密碼測試。
使用多執行緒加速掃描。
生成專業風格的漏洞報告。

資安概念：
漏洞掃描：發現系統弱點。
弱密碼攻擊：展示密碼安全的重要性。
道德駭客：強調合法測試的倫理。
步驟：
在我的電腦Kali Linux VM 上執行程式碼。
$ python 3vulnerability_hunter.py。


<img width="921" height="832" alt="image" src="https://github.com/user-attachments/assets/87e71ccb-cc56-491b-ae54-a805561e9fbc" />


<img width="1363" height="648" alt="image" src="https://github.com/user-attachments/assets/6c3b5284-a4b6-471e-97be-1f5a4214ce5d" />




本節心得:結合了3.1/3.2程式碼，掃瞄有開放SSH的電腦(Teacher1VM)，當然在另一台電腦上事先創立了一個使用者帳號(密碼為123456)
本程式發現SSH可能的漏洞，並進行密碼暴力破解且成功；並且可生成一個簡單的分析報告





反思與收穫
這次的實驗讓我首次深入了解網路運作原理和資安概念，尤其是如何使用虛擬機進行安全測試，避免對真實系統造成影響。我學會了如何尋找網路地址、確認網路連接，並進行端口掃描與密碼破解。這不僅讓我了解資安的攻擊方式，也讓我認識到防範的重要性，尤其是密碼管理與漏洞掃描。
這次經驗讓我體會到資安不僅是技術問題，更涉及倫理與責任。在學習的過程中，我不僅掌握了基本技能，還學會了如何合法、負責任地進行資安測試。未來，我會持續提升自己的資安意識與技術能力，為應對不斷變化的網路威脅做好準備。




參考資料
快速linux指令教學（mac, windows, ubuntu...等等適用）｜工程師必備技能
快速linux指令教學（mac, windows, ubuntu...等等適用）｜工程師必備技能
拯救資工系學生的基本素養—SSH & SCP 連線到遠端機器  
