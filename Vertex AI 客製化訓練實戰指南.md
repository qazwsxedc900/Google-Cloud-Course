<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## Vertex AI 客製化訓練實戰指南

Google Cloud 的 Vertex AI 提供高度靈活的客製化訓練方案，讓開發者能完全掌控機器學習流程。以下是從環境選擇到實作的深度解析：

---

### **一、核心訓練架構比較**

| 元件 | 功能特性 | 適用場景 |
| :-- | :-- | :-- |
| **Custom Job** | 單一訓練任務執行，支援單節點/分散式運算 | 標準模型訓練、快速實驗 |
| **Hyperparameter Tuning Job** | 自動化超參數優化，支援並行試驗與指標追蹤 | 模型效能調校、參數空間探索 |
| **Training Pipeline** | 整合資料載入、訓練、模型上傳的全流程自動化 | 生產級別流水線、週期性再訓練 |


---

### **二、訓練環境選擇策略**

#### **預建容器 vs. 自訂容器**

| 維度 | 預建容器 | 自訂容器 |
| :-- | :-- | :-- |
| **啟動速度** | 即時可用，無需建置映像檔 | 需自行打包並上傳至 Artifact Registry |
| **框架支援** | 官方支援 TensorFlow/PyTorch/XGBoost 等主流框架 | 可任意組合程式庫與依賴項 |
| **維護成本** | Google 負責安全更新與版本兼容性 | 需自行管理相依套件與漏洞修補 |
| **客製化程度** | 受限於預裝套件，可透過 `pip install` 追加部分依賴 | 完全掌控環境配置，適合特殊硬體需求或私有套件 |

> **選型建議**：優先使用預建容器加速開發，僅在以下情況選擇自訂容器：
> - 需特定 Linux 套件或驅動程式
> - 使用非主流 ML 框架（如 MXNet）
> - 企業內部有標準化基礎映像檔

---

### **三、實作流程解析**

#### **步驟 1：準備訓練程式碼**

```python
# 範例 PyTorch 訓練腳本 (trainer/task.py)
import torch
from torch.utils.data import DataLoader

def train_model(data_path, epochs=10):
    dataset = CustomDataset(data_path)
    loader = DataLoader(dataset, batch_size=32)
    model = Model()
    optimizer = torch.optim.Adam(model.parameters())
    
    for epoch in range(epochs):
        for batch in loader:
            outputs = model(batch)
            loss = criterion(outputs, batch.labels)
            loss.backward()
            optimizer.step()
```


#### **步驟 2：容器化程式碼**

```dockerfile
# 使用預建 PyTorch 容器
FROM gcr.io/cloud-aiplatform/training/pytorch-gpu.1-9:latest

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

ENTRYPOINT ["python", "-m", "trainer.task"]
```


#### **步驟 3：提交訓練任務**

```bash
# 使用 gcloud 指令建立分散式訓練任務
gcloud ai custom-jobs create \
  --region=us-central1 \
  --display-name="custom-pytorch-training" \
  --worker-pool-spec=machine-type=n1-standard-16,replica-count=1,accelerator-type=NVIDIA_TESLA_T4,accelerator-count=2,container-image-uri="gcr.io/cloud-aiplatform/training/pytorch-gpu.1-9:latest" \
  --args="--data-path=gs://my-bucket/data,--epochs=50"
```


---

### **四、進階應用場景**

#### **超參數自動調優**

```yaml
# hyperparameter.yaml
studySpec:
  metrics:
  - metricId: "accuracy"
    goal: "MAXIMIZE"
  parameters:
  - parameterId: "learning_rate"
    doubleValueSpec:
      minValue: 0.001
      maxValue: 0.1
  - parameterId: "batch_size"
    discreteValueSpec:
      values: [32, 64, 128]
```

```bash
# 啟用超參數調優
gcloud ai hpt-tuning-jobs create \
  --config=hyperparameter.yaml \
  --worker-pool-spec=...
```


#### **分散式訓練配置**

```python
# 使用 TensorFlow Distribution Strategies
strategy = tf.distribute.MirroredStrategy()
with strategy.scope():
    model = create_model()
    model.compile(optimizer='adam', loss='sparse_categorical_crossentropy')
```


---

### **五、監控與優化建議**

1. **資源利用率追蹤**
    - 啟用 **Cloud Monitoring** 監控 GPU 使用率與記憶體消耗
    - 設定警示規則：當 GPU 利用率低於 60% 時觸發通知
2. **成本控制策略**
    - 混合使用常規 VM 與 Preemptible 實例（節省最高 80% 成本）
    - 啟用 **Early Stopping** 避免無效訓練週期
3. **效能調校技巧**
    - 使用 **TFProfiler** 或 **PyTorch Profiler** 分析訓練瓶頸
    - 啟用 **Mixed Precision Training** 加速計算並減少記憶體消耗

---

### **六、模型部署流程**

1. **註冊模型至 Vertex AI Model Registry**

```bash
gcloud ai models upload \
  --region=us-central1 \
  --display-name="production-model" \
  --artifact-uri="gs://my-bucket/model/"
```

2. **部署至線上服務端點**

```python
from google.cloud import aiplatform

endpoint = aiplatform.Endpoint.create(display_name="live-predictor")
model.deploy(
    endpoint=endpoint,
    machine_type="n1-standard-4",
    accelerator_type="NVIDIA_TESLA_T4",
    accelerator_count=1
)
```


---

### **總結**

Vertex AI 客製化訓練提供從實驗到生產的完整工具鏈。透過合理選擇容器策略與資源配置，可大幅提升模型開發效率。結合超參數調優與分散式訓練，能在保證模型品質的同時優化運算成本。實際部署時建議搭配 MLOps 工具實現持續訓練與監控。

<div style="text-align: center">⁂</div>

[^1]: https://cloud.google.com/vertex-ai/docs/training/pre-built-containers

[^2]: https://expertbeacon.com/unlock-the-full-potential-of-ai-with-vertex-ai-custom-training-jobs/

[^3]: https://codelabs.developers.google.com/vertex_custom_training_prediction

[^4]: https://cloud.google.com/vertex-ai/docs/training/custom-training-methods

[^5]: https://cloud.google.com/vertex-ai/docs/training/containers-overview

[^6]: https://cloud.google.com/vertex-ai/docs/training/configure-container-settings

[^7]: https://cloud.google.com/vertex-ai/docs/training/overview

[^8]: https://www.toolify.ai/ai-news/master-machine-learning-with-a-custom-ml-model-84579

[^9]: https://codelabs.developers.google.com/vertex-p2p-training

[^10]: https://codelabs.developers.google.com/vertex-workbench-intro

[^11]: https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/prediction/pytorch_train_deploy_models_with_prebuilt_containers.ipynb

[^12]: https://www.cloudskillsboost.google/focuses/73258?parent=catalog

[^13]: https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/custom/custom_training_container_and_model_registry.ipynb

[^14]: https://www.googlecloudcommunity.com/gc/AI-ML/Custom-container-in-vertex-workbench/td-p/430464

[^15]: https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/training/multi_node_ddp_nccl_vertex_training_with_custom_container.ipynb

[^16]: https://www.cloudskillsboost.google/course_templates/17/labs/504888?locale=zh_TW

[^17]: https://www.cloudskillsboost.google/course_templates/593/video/541292?locale=pl

[^18]: https://www.cloudskillsboost.google/paths/17/course_templates/17/labs/504888

[^19]: https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/main/notebooks/official/prediction/pytorch_image_classification_with_prebuilt_serving_containers.ipynb

[^20]: https://www.googlecloudcommunity.com/gc/AI-ML/Deployed-custom-container-to-vertex-but-container-is-unable-to/m-p/775989

[^21]: https://www.youtube.com/watch?v=ZCqDDJLkCFY

[^22]: https://cloud.google.com/vertex-ai/docs/predictions/use-custom-container

[^23]: https://www.youtube.com/watch?v=VRQXIiNLdAk

