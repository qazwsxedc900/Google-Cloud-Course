<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## 網路七層級（OSI 模型）概念整理

在雲端網路與 Google Cloud 架構學習中，理解 OSI 七層模型（Open Systems Interconnection Model）有助於掌握網路通訊的分層結構與各層功能。以下是七層模型的簡要說明與每層重點[^1][^2][^3][^4][^5][^6][^7][^8]：

---

### **OSI 七層模型總覽**

| 層級（Layer） | 名稱（中文/英文） | 主要功能與範例 | 典型協定/元件 |
| :--: | :-- | :-- | :-- |
| 7 | 應用層 Application | 使用者與網路應用互動，提供應用服務 | HTTP, SMTP, FTP, DNS |
| 6 | 表示層 Presentation | 資料格式轉換、加解密、壓縮 | SSL/TLS, JPEG, ASCII |
| 5 | 會議層 Session | 管理通訊會話、建立/維持/終止連線 | RPC, NetBIOS |
| 4 | 傳輸層 Transport | 負責資料分段、可靠傳輸、錯誤修正、流量控制 | TCP, UDP |
| 3 | 網路層 Network | 路由選擇、邏輯位址（IP）、跨網段傳輸 | IP, ICMP, OSPF, BGP |
| 2 | 資料連結層 Data Link | 資料封裝成 frame、MAC 位址、錯誤偵測 | Ethernet, ARP, PPP |
| 1 | 實體層 Physical | 傳輸實體位元（bit）、線路、電壓、連接器 | 光纖、雙絞線、無線、USB |


---

### **各層詳細說明**

#### **7. 應用層（Application Layer）**

- 提供使用者與應用程式直接互動的介面。
- 例如：瀏覽器使用 HTTP 協定存取網頁、Outlook 用 SMTP 傳送郵件[^2][^5][^7][^8]。


#### **6. 表示層（Presentation Layer）**

- 負責資料格式轉換（如字元編碼）、加密/解密、壓縮/解壓縮。
- 確保不同系統間資料可理解與安全傳遞[^2][^5][^7][^8]。


#### **5. 會議層（Session Layer）**

- 管理裝置間的會話連線，負責建立、維持、結束通訊會話。
- 支援同步點、斷線重連與認證[^2][^5][^7][^8]。


#### **4. 傳輸層（Transport Layer）**

- 提供端對端的資料傳輸、分段與重組、錯誤偵測與修正、流量控制。
- 主要協定：TCP（可靠）、UDP（快速但不保證可靠）[^2][^4][^5][^7][^8]。


#### **3. 網路層（Network Layer）**

- 處理跨多個網路的資料路由與邏輯位址（如 IP 位址）分配。
- 負責選擇最佳路徑將資料包送達目的地[^3][^4][^5][^7][^8]。


#### **2. 資料連結層（Data Link Layer）**

- 將資料包封裝成 frame，管理 MAC 位址、錯誤偵測與簡單修正。
- 控制同一網段內的資料傳輸與碰撞管理（如乙太網）[^1][^3][^4][^5][^7][^8]。


#### **1. 實體層（Physical Layer）**

- 負責實際傳輸 bit（0/1）訊號，規範線材、電壓、連接器等硬體規格。
- 包含光纖、雙絞線、無線電波等傳輸媒介[^1][^3][^4][^5][^7][^8]。

---

### **應用於雲端與 GCP 架構的意義**

- **GCP VPC 網路**：涵蓋第1~3層（實體、資料連結、網路層）與第4層（傳輸層），並向上支援應用層協定（如 HTTP/HTTPS）。
- **防火牆規則**：常根據第4層（TCP/UDP埠號）與第3層（IP位址）設計。
- **負載平衡**：分為第4層（網路負載平衡）與第7層（應用負載平衡），分別處理不同協定與應用場景。

---

### **記憶口訣**

- 「請（實體）大（資料連結）網（網路）傳（傳輸）會（會議）表（表示）應（應用）」
或
- 「All People Seem To Need Data Processing」
(Application, Presentation, Session, Transport, Network, Data Link, Physical)

---

這個七層模型是學習網路、雲端架構與資安的基礎，能幫助你分析各種網路問題與設計安全、有效率的雲端解決方案[^1][^2][^3][^4][^5][^6][^7][^8]。

<div style="text-align: center">⁂</div>

[^1]: https://aws.amazon.com/what-is/osi-model/

[^2]: https://www.techtarget.com/searchnetworking/definition/OSI

[^3]: https://en.wikipedia.org/wiki/OSI_model

[^4]: https://talent500.com/blog/what-is-osi-model-and-its-layers/

[^5]: https://www.corero.com/what-is-the-osi-model/

[^6]: https://www.a1.digital/knowledge-hub/osi-model-and-its-7-layers-explained/

[^7]: https://www.digitalguardian.com/blog/what-osi-model-overview-osi-models-7-layers

[^8]: https://nordlayer.com/learn/other/guide-to-osi-model/

[^9]: https://www.imperva.com/learn/application-security/osi-model/

[^10]: https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/

