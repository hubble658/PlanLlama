# 🔧 PlanLLaMA — Teknik Mimari & Pipeline

Bu belge, PlanLLaMA'nın **AI çekirdeği**, **servis topolojisi** ve **üretim akışları** hakkında pratik/uygulanabilir teknik bilgiyi içerir. Uygulamayı üretime taşırken veya yerelde çoğaltırken referans alın.

---

## 1) Mimari Genel Bakış

```  
Proje Dokümanı (Türkçe)            ┌────────────────────┐
         │───────────────────────→ │  Doküman Enhancing │ 
         │                         │     Model 1 (DPO)  │  
         ↓                         │  Fine-tuned Llama  │
┌────────────────────┐             └──────────┬─────────┘                                
│   Görev Planlama   │←───────────────────────▼           
│   Model 1 (DPO)    │    
│  Turkish-Llama-8B  │                      
└─────────┬──────────┘                      
          │
          ↓
    Markdown Plan
          │
          ↓
┌────────────────────┐
│  Model 2 (SFT)     │  ← JSON Dönüşümü
│  Fine-tuned Llama  │
└─────────┬──────────┘
          │
          ↓        
    Jira JSON Format
          │
          ↓                   ┌────────────────────┐
    Backend Database ←──────→ │    Proje Analizi   │ 
                              │     Model 2 (SFT)  │
                              │  Turkish-Llama-8B  │
                              └────────────────────┘
```

- **Model 1 (DPO)**: Türkçe proje dokümanlarından **Markdown görev planı** üretir.  
- **Model 2 (Instruct/SFT)**: Markdown planı **Jira JSON** formatına dönüştürür.  
- **Backend (Flask)**: JSON’u kalıcı hale getirir, analitik ve CRUD sağlar.  
- **Frontend (React)**: Dashboard, Kanban ve AI entegrasyon ekranları.

---

## 2) Model Sunumu (Serving)

- **Çerçeve:** `vLLM` (OpenAI-uyumlu REST API)
- **Modeller:**
  - `ytu-ce-cosmos/Turkish-Llama-8b-DPO-v0.1` → Görev planlama / TR analiz
  - `Berkesule/kodllama_sft_cosmosllama_merged` → Markdown → Jira JSON dönüşümü
- **Örnek çalıştırma:**
  ```bash
  vllm serve ytu-ce-cosmos/Turkish-Llama-8b-DPO-v0.1 \
    --trust-remote-code --dtype auto --max-model-len 8192 \
    --gpu-memory-utilization 0.90 --tensor-parallel-size 1 \
    --disable-custom-all-reduce --enforce-eager
### Backend Database

3) İstemci (Client) Erişimi

OpenAI-compatible SDK ile çağrı:

from openai import OpenAI
client = OpenAI(
    base_url=f"{BASE_URL.rstrip('/')}/v1",
    api_key="sk-local-not-needed"
)


Timeout ve retry politikalarını ekleyin.

Uzun girdilerde max_tokens ve bağlam (context window) limitlerine dikkat edin.

4) İş Akışları (Pipelines)
A) Çeviri (opsiyonel veri ön işleme)

Girdi Kolonları: Project_description, Project_plan

Çıktı: project_plan_final_data_turkish.csv (ek kolonlar: *_TR)

B) Açıklama Üretimi (TR)

Project_plan_TR → akıcı Türkçe açıklama (project_plan_final_data_turkish_sum.csv)

C) Görev Planlama (Model 1 — DPO)

Türkçe dokümanı Markdown task list’e dönüştürür.

Chunking: 7+ task olduğunda otomatik bölme → bellek/latency optimizasyonu.

D) JSON Dönüşümü (Model 2 — Instruct/SFT)

Markdown → Jira JSON alan eşlemeleri:

Tarih: “15 Kasım 2025” → YYYY-MM-DD

Süre: “3 gün” → 3d

Öncelik: “Yüksek/Orta/Düşük” → High/Medium/Low

E) (Opsiyonel) CV Analizi

Serbest metin CV → skills, interests, spoken_languages + evidence ve confidence içeren JSON.

5) Performans & Kaynak Kullanımı

2 model toplam GPU ≈ 14–16 GB (quant/precision’a bağlı)

7 task ≈ 3–5 sn, 20+ task ≈ ~20 sn

Bağlam: --max-model-len 8192 (artarsa gecikme artar)

Yük altında batching ve request deduplication ekleyin. Üretimde observability (logs/metrics/traces) kritik.
