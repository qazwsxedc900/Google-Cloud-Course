<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## 什麼是虛擬私有雲（Virtual Private Cloud, VPC）？

**虛擬私有雲（VPC）**是一種在公有雲平台（如 Google Cloud）上建立的、隔離且私有的虛擬網路環境。你可以把它想像成在共享的雲端基礎設施中，劃分出一塊專屬於你的「雲中內網」，讓你能安全地部署、連接和管理雲端資源[^1][^2][^3][^5][^6][^8]。

---

### **VPC 的主要功能與特性**

- **資源串接與隔離**
VPC 負責連接雲端上的各種資源（如 VM、容器、App Engine 等），同一個 VPC 內的資源能透過內部 IP 互相溝通，而不同 VPC 則預設隔離[^2][^3][^6][^8]。
- **全球性與彈性**
Google Cloud 的 VPC 屬於全球性資源，可跨多個區域（region）與可用區（zone）部署子網路（subnet），並能動態擴展 IP 範圍而不中斷現有服務[^1][^6]。
- **安全性**
透過防火牆規則、路由表、VPC Service Controls 等功能，保護你的資源並防止未授權存取[^1][^2][^3]。
- **可擴展性**
可以隨需求調整子網路大小、IP 範圍，支援大規模部署與多專案共享[^1][^6]。
- **連接性**
支援與本地資料中心的 VPN 或專線互連（Interconnect），也可設定 VPC Peering 進行多 VPC 之間的私有通訊[^1][^2][^3]。

---

### **VPC 網路架構與元件**

| 元件 | 說明 |
| :-- | :-- |
| VPC 網路 | 隔離的虛擬網路，管理 IP、路由、防火牆等設定。 |
| 子網路（Subnet） | VPC 內的邏輯分區，通常對應不同區域或功能，資源可部署於不同子網路。 |
| 路由表 | 控制網路流量的轉送路徑，VPC 內建路由表無需額外管理[^7]。 |
| 防火牆規則 | 控制進出 VPC 資源的流量，提升安全性。 |
| VPC Flow Logs | 即時記錄網路流量資訊，便於監控、分析與資安稽核[^1]。 |
| VPN/Interconnect | 與本地資料中心或其他雲端網路安全連接。 |


---

### **VPC 網路的兩種建立模式**

| 模式 | 說明 | 適用情境 |
| :-- | :-- | :-- |
| 自動子網路模式（Auto） | 建立 VPC 時自動在每個區域建立子網路，適合測試、概念驗證，彈性較低。 | 測試、快速啟動 |
| 自訂子網路模式（Custom） | 完全自訂子網路與 IP 範圍，適合生產環境，彈性高。 | 生產環境、複雜架構 |

> **注意：自動模式 VPC 可轉換為自訂模式，但無法反向轉換。**

---

### **VPC 實際應用範例**

- 在 VPC 中建立多個子網路，將不同服務部署於不同區域，提升可用性與容錯能力。
- 透過 VPC Peering 或 VPN，實現多個 VPC 或本地資料中心間的安全連接。
- 利用防火牆規則與路由表，細緻控管資源間的存取權限與流量路徑。

---

### **總結**

VPC 是雲端架構中不可或缺的基礎設施，提供安全、彈性、可擴展的網路環境，讓你能靈活管理雲端資源並確保資料隔離與安全[^1][^2][^3][^6][^8]。

<div style="text-align: center">⁂</div>

[^1]: https://cloud.google.com/vpc

[^2]: https://cloud.google.com/vpc/docs/vpc

[^3]: https://mile.cloud/zh/resources/blog/vpc-introduction-network-setting-subnet_533

[^4]: https://codelabs.developers.google.com/ncc-vpc-as-spoke

[^5]: https://zh.wikipedia.org/zh-tw/虛擬私有雲

[^6]: https://www.cloudskillsboost.google/paths/71/course_templates/522/labs/410205?locale=zh_TW

[^7]: https://www.cloudskillsboost.google/paths/14/course_templates/60/video/461505?locale=zh_TW

[^8]: https://zh-cn.eitca.org/云计算/eitc-cl-gcp谷歌云平台/gcp网络/虚拟私有云vpc/考试复习虚拟私有云vpc/什么是虚拟私有云-VPC，它如何为-Google-云平台-GCP-上的资源提供托管网络功能/

