<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## VPC 內部流量控制：路由與防火牆規則解析

在 Google Cloud 的 VPC 中，**路由（Routes）** 與 **防火牆規則（Firewall Rules）** 共同協作，實現流量的精準控制與安全隔離。以下是兩者的運作機制與協作方式：

---

### **一、路由（Routes）的作用與機制**

#### **路由表的核心功能**

- 決定流量從 VM 實例發送後的**下一跳（Next Hop）**，如：其他子網路、VPN 閘道、內部負載平衡器等。
- Google Cloud 的路由表**無需手動維護**，系統會自動管理並分發到所有 VM 控制器[^1]。


#### **路由選擇規則**

- **最長前綴匹配（Longest Prefix Match）**：優先選擇 CIDR 範圍最精確的路由。
例如：`10.0.1.0/24` 比 `10.0.0.0/16` 更優先匹配目標 IP `10.0.1.5`[^2]。
- **路由類型優先級**：
靜態路由（如 VPC Peering） > BGP 動態路由（如 VPN）[^2]。


#### **VPC 路由的實際應用**

```markdown
| 目的地 CIDR       | 下一跳類型       | 說明                          |
|------------------|----------------|-----------------------------|
| 10.0.0.0/16      | local          | 同一 VPC 內流量，直接內部路由        |
| 192.168.0.0/24   | vpn-gateway    | 透過 VPN 連接到本地資料中心          |
| 0.0.0.0/0        | internet-gateway | 預設路由，將非內部流量導向網際網路        |
```


---

### **二、防火牆規則（Firewall Rules）的運作**

#### **核心特性**

- **狀態化（Stateful）**：允許已建立的連線雙向通行，無需額外規則[^5]。
- **全域分散式**：規則定義在 VPC 層級，自動套用到所有相關 VM[^5]。
- **預設規則**：
    - **隱性允許出站**：所有出站流量預設放行。
    - **隱性拒絕入站**：所有入站流量預設阻擋，需明確允許[^4]。


#### **規則定義要素**

| 要素 | 說明 |
| :-- | :-- |
| 方向（Direction） | `ingress`（入站）或 `egress`（出站） |
| 來源/目標 | 可指定 IP 範圍、VM 標籤（Tags）、服務帳號（Service Accounts） |
| 協定與埠號 | 如 `tcp:80-443`、`udp:53`、`icmp` 等 |
| 動作（Action） | `allow` 或 `deny` |

#### **標籤（Tags）應用範例**

```bash
# 建立允許 HTTP/HTTPS 流量的防火牆規則（使用標籤 "web-server"）
gcloud compute firewall-rules create allow-web \
    --direction=INGRESS \
    --action=ALLOW \
    --rules=tcp:80,tcp:443 \
    --target-tags=web-server
```

- 此規則允許**所有帶有 `web-server` 標籤的 VM** 接收 80/443 埠的 TCP 流量[^3][^5]。

---

### **三、路由與防火牆的協作流程**

1. **流量發起**
VM 實例發送封包時，根據**路由表**決定下一跳目標。
2. **防火牆檢查**
    - **出站流量**：檢查 `egress` 規則，決定是否允許傳送。
    - **入站流量**：檢查 `ingress` 規則，決定是否允許接收。
3. **實例間通信範例**
    - **情境**：VM-A（標籤 `db`）嘗試連線到 VM-B（標籤 `web`）的 3306 埠。
    - **路由**：若兩者在同一 VPC，路由表會導向 `local`，直接內部傳輸。
    - **防火牆**：需在 VM-B 的 `ingress` 規則中允許來源標籤 `db` 且目標埠 3306。

---

### **四、最佳實踐與注意事項**

- **最小權限原則**：僅開放必要埠，避免使用過大 IP 範圍（如 `0.0.0.0/0`）[^5]。
- **標籤管理**：用標籤取代 IP 位址，提升規則可讀性與擴充性。
- **日誌與監控**：啟用 **Firewall Rules Logging** 追蹤規則匹配情況，並使用 **Firewall Insights** 優化配置[^5]。
- **路由衝突處理**：定期檢查重疊 CIDR，避免非預期路由選擇。

---

### **總結**

路由決定「流量如何走」，防火牆控制「流量能否走」。兩者結合確保 VPC 內部流量的高效傳輸與嚴格安全管控。透過標籤化規則與自動化路由管理，可大幅簡化雲端網路維運複雜度。

<div style="text-align: center">⁂</div>

[^1]: https://cloud.google.com/vpc/docs/routes

[^2]: https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html

[^3]: https://cloud.google.com/firewall/docs/using-firewalls

[^4]: https://jayendrapatil.com/tag/vpc-firewall-rules/

[^5]: https://cloud.google.com/firewall/docs/firewalls

[^6]: https://www.youtube.com/watch?v=Mq944Rrd9ao

[^7]: https://www.youtube.com/watch?v=ksJWwkG_p4M

[^8]: https://docs.mulesoft.com/cloudhub/vpc-firewall-rules-concept

[^9]: https://cloud.google.com/vpc/docs/add-remove-network-tags

[^10]: https://docs.aws.amazon.com/vpc/latest/userguide/RouteTables.html

[^11]: https://www.alibabacloud.com/help/en/vpc/user-guide/network-traffic-management-using-custom-routing-tables

[^12]: https://cloud.ibm.com/docs/vpc?topic=vpc-about-custom-routes

[^13]: https://price2meet.com/gcp/docs/compute_docs_vpc_routes.pdf

[^14]: https://cloud.ibm.com/docs/vpc?topic=vpc-create-vpc-route

[^15]: https://docs.otc.t-systems.com/virtual-private-cloud/umn/service_overview/basic_concepts/route_table.html

[^16]: https://www.cloudskillsboost.google/paths/15/course_templates/21/video/513507?locale=zh

[^17]: https://mile.cloud/zh/resources/blog/vpc-network-firewall-security-setting_567

[^18]: https://www.youtube.com/watch?v=8pqwoZflqPo

[^19]: https://rnemet.dev/posts/gcp/gcp_tf_vpc_firewall_2/

[^20]: https://www.youtube.com/watch?v=eRkQG1U4Zw4

