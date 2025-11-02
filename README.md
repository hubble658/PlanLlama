# 🦙 PlanLLaMA - AI Destekli Proje Yönetim Platformu

> **Llama Hackathon 2025 - KodLlama Takımı**  
> Türkçe proje dokümanlarını otomatik olarak görevlere (task) dönüştüren, AI destekli akıllı proje yönetim sistemi.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![React](https://img.shields.io/badge/react-18.0+-61DAFB.svg)](https://reactjs.org/)

---

## 📋 İçindekiler

- [Proje Hakkında](#-proje-hakkında)
- [Özellikler](#-özellikler)
- [Mimari](#-mimari)
- [Teknik Mimari & Pipeline](./TECHNICAL_README.md)
- [Kurulum](#-kurulum)
- [Kullanım](#-kullanım)
- [Repolar](#-repolar)
- [Teknolojiler](#-teknolojiler)
- [Ekip](#-ekip)

---

## 🎯 Proje Hakkında

**PlanLLaMA**, proje yönetiminde yapay zeka gücünü kullanarak ekiplerin iş akışlarını optimize eden bir platformdur. Türkçe proje dokümanlarını analiz ederek otomatik olarak:

- **Görevleri** (Tasks) oluşturur
- **Ekip üyelerine** yeteneklerine göre görev atar
- **Jira entegrasyonuna uygun** JSON çıktısı üretir

### 🌟 Neden PlanLLaMA?

- ⏱️ **Zaman Tasarrufu**: Manuel proje planlaması yerine otomatik görev oluşturma
- 🎯 **Akıllı Atama**: Ekip üyelerinin becerilerine göre optimal görev dağılımı
- 🇹🇷 **Türkçe Destek**: Türkçe tarih, öncelik ve süre ifadelerini otomatik çevirir
- 📊 **Gerçek Zamanlı Takip**: Proje ilerlemesini ve ekip yükünü anlık görüntüleme
- 🔄 **Entegrasyon Hazır**: Üretilen task'lar Jira'ya entegre edilebilir yapıda

---

## ✨ Özellikler

### 🤖 AI Motoru
- **Dual Model Sistemi**: 
  - Model 1 (DPO): Proje analizi ve görev planlama
  - Model 2 (Instruct/SFT): Markdown'dan JSON dönüşümü
- **Chunking Desteği**: 7+ task için otomatik gruplama ile GPU bellek optimizasyonu
- **Smart Parsing**: Türkçe ifadelerin otomatik İngilizce Jira formatına çevrilmesi  
  - "15 Kasım 2025" → `"2025-11-15"`  
  - "3 gün" → `"3d"`  
  - "Yüksek" → `"High"`
- **Hızlı İşlem**: 7 task için ~3-5 saniye, 20+ task için ~20 saniye
- **Proje Durumuna Göre Anlık Analiz**
- **Proje Açıklaması Zenginleştirme**

### 📊 Backend API
- **RESTful API**: Flask tabanlı modern API
- **PostgreSQL/SQLite**: Güvenli veri saklama
- **CRUD İşlemleri**: Projects, Tasks, Employees için tam CRUD desteği
- **İstatistikler**: Proje ve ekip analitikleri
- **Filtreleme**: Status, priority, assignee bazlı sorgulama

### 💻 Frontend Arayüz
- **Modern UI**: React + Vite ile hızlı ve responsive tasarım
- **Dashboard**: Proje ve görev özeti
- **Kanban Board**: Görsel görev takibi
- **Team Management**: Ekip üyesi yönetimi ve workload görüntüleme
- **AI Integration**: Doküman yükleme ve otomatik task oluşturma
  

---

## 🏗️ Mimari

```
┌─────────────────────────────────────────────────────────────┐
│                     PlanLLaMA Platform                       │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
        ┌─────▼─────┐   ┌─────▼─────┐   ┌────▼─────┐
        │  Frontend  │   │  Backend  │   │ AI Core  │
        │   React    │◄──┤   Flask   │◄──┤  vLLM    │
        │   Vite     │   │PostgreSQL │   │  Models  │
        └────────────┘   └───────────┘   └──────────┘
```

> Detaylı mimari ve üretim pipeline'ı için bkz. **[TECHNICAL_README.md](./TECHNICAL_README.md)**.

---

## 🚀 Kurulum

### Ön Gereksinimler

- **Python**: 3.8+
- **Node.js**: 18+
- **CUDA**: 11.8+ (GPU için)
- **GPU Memory**: Minimum 16GB (Her model ~7GB)
- **PostgreSQL**: 13+ (veya SQLite development için)

### 1️⃣ AI Motoru Kurulumu

```bash
# Repo'yu klonlayın
git clone https://github.com/berkesule/Llama-Hackathon-KodLlama-Takimi-ai-Dokumantasyonu.git
cd Llama-Hackathon-KodLlama-Takimi-ai-Dokumantasyonu

# Sanal ortam oluşturun
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Bağımlılıkları yükleyin
pip install torch vllm flask pyngrok

# Ngrok token'ınızı ayarlayın
ngrok config add-authtoken YOUR_NGROK_TOKEN

# Servisi başlatın
python llm_powered_project_planner.py

Çıktı:

```
✅ Model 1 yüklendi!
✅ Model 2 yüklendi!
📡 Ngrok URL: https://xxxx-xx-xxx.ngrok.io
🔗 API Endpoint: https://xxxx-xx-xxx.ngrok.io/api/generate
```

### 2️⃣ Backend Kurulumu

```bash
# Repo'yu klonlayın
git clone https://github.com/hubble658/planllama-backend.git
cd planllama-backend

# Sanal ortam oluşturun
python -m venv venv
source venv/bin/activate

# Bağımlılıkları yükleyin
pip install -r requirements.txt

# .env dosyasını yapılandırın
cp .env.example .env
# .env içinde DATABASE_URL ve diğer ayarları düzenleyin

# Veritabanını başlatın
python init_db.py --seed

# Servisi başlatın
python app.py
```

Backend: `http://localhost:5000`


### 3️⃣ Frontend Kurulumu

```bash
# Repo'yu klonlayın
git clone https://github.com/hubble658/planllama-frontend.git
cd planllama-frontend

# Bağımlılıkları yükleyin
npm install

# Development sunucusunu başlatın
npm run dev
```

Frontend: `http://localhost:5173`

---

## 💡 Kullanım

### API ile Task Oluşturma

```python
import requests

payload = {
    "json_input": {
        "project_title": "E-Ticaret Platformu",
        "project_description": "Modern bir e-ticaret sistemi geliştirme projesi...",
        "possible_solution": "Mikroservis mimarisi, Django + React",
        "team": [
            {
                "employee_id": "e14",
                "name": "Ahmet Yılmaz",
                "skills": ["python", "django", "postgresql"],
                "department": "Backend"
            },
            {
                "employee_id": "e07",
                "name": "Ayşe Demir",
                "skills": ["react", "typescript", "css"],
                "department": "Frontend"
            }
        ],
        "metadata": {
            "description": "E-Ticaret MVP",
            "company": "TechCorp",
            "year": 2025
        }
    },
    "project_key": "ECOM",
    "use_model": False  # True: Model+Parse, False: Direkt Parse
}

response = requests.post(
    "https://your-ngrok-url/api/generate",
    json=payload
)

result = response.json()
print(f"✅ Toplam {result['total_tasks']} task oluşturuldu!")
```

### Örnek Çıktı

```json
{
  "success": true,
  "method": "direct_parse",
  "total_tasks": 12,
  "jira_json": {
    "tasks": [
      {
        "fields": {
          "project": {"key": "ECOM"},
          "summary": "API Gateway Kurulumu",
          "assignee": {"name": "Ahmet Yılmaz", "accountId": "e14"},
          "priority": {"name": "High"},
          "duedate": "2025-11-15",
          "timetracking": {"originalEstimate": "3d"}
        }
      }
    ]
  }
}
```

---

## 📦 Repolar

| Repo | Açıklama | Teknolojiler |
|------|----------|--------------|
| [**AI Dokümantasyonu**](https://github.com/berkesule/Llama-Hackathon-KodLlama-Takimi-ai-Dokumantasyonu) | AI motoru, model eğitimi ve data pipeline | Python, vLLM, PyTorch, LoRA |
| [**Backend**](https://github.com/hubble658/planllama-backend) | REST API, veritabanı ve iş mantığı | Flask, PostgreSQL, SQLAlchemy |
| [**Frontend**](https://github.com/hubble658/planllama-frontend) | Kullanıcı arayüzü ve dashboard | React, Vite, TailwindCSS |

---

## 🛠️ Teknolojiler

### AI & Machine Learning
- **vLLM**: Hızlı LLM inference
- **Turkish-Llama-8B-DPO**: Görev planlama modeli
- **Turkish-Llama-8B-Instruct**: JSON dönüşüm modeli
- **Fine-tuned Model**: `Berkesule/kodllama_sft_cosmosllama_merged`
- **LoRA**: Efficient fine-tuning
- **PyTorch**: Deep learning framework

### Backend
- **Flask**: Web framework
- **SQLAlchemy**: ORM
- **PostgreSQL**: Production database
- **SQLite**: Development database
- **Flask-Migrate**: Database migrations
- **Flask-CORS**: Cross-origin support

### Frontend
- **React 18**: UI library
- **Vite**: Build tool
- **React Router**: Routing
- **Context API**: State management
- **Axios**: HTTP client

### DevOps
- **Ngrok**: Public URL tunneling
- **Docker**: Containerization
- **Gunicorn**: WSGI server

---

## 📊 Performans

| Metrik | Değer |
|--------|-------|
| Task Üretme Süresi | ~20-30 saniye (ör:8 task) |
| GPU Bellek Kullanımı | ~14-16GB (2 model) |
| Maksimum Task | Sınırsız (chunking) |

---

## 🎓 Model Eğitimi

Projede kullanılan AI modelleri, 3 aşamalı bir pipeline ile eğitilmiştir:

### 1. Ekip Üretimi
Kaggle'dan alınan proje planları kullanılarak her proje için 4 farklı ekip kombinasyonu oluşturulur.

### 2. Görev Planlama
Her ekip için Turkish-Llama-8B-DPO modeli ile görev planları (Markdown format) üretilir.

### 3. JSON Dönüşümü
Fine-tuned Llama modeli ile Markdown planlar Jira JSON formatına dönüştürülür.

**Dataset**: Sentetik olarak üretilmiş 1000+ proje planı  
**Fine-tuning**: LoRA (r=4, alpha=8) ile 3 epoch  

---

## 👥 Ekip

**KodLlama Takımı** 
- Osman Orçun Aydın ([@orcnnn](https://github.com/orcnnn))
- Habil Çoban ([@hubble658](https://github.com/hubble658)) 
- Berke Bünyamin Süle ([@berkesule](https://github.com/berkesule)) 
- Yavuz Selim Aygan ([@cxrbon16](https://github.com/cxrbon16))


Llama Hackathon 2025 için geliştirilmiştir.

---

## 📄 Lisans

Bu proje MIT lisansı altında lisanslanmıştır. Detaylar için [LICENSE](LICENSE) dosyasına bakınız.

---

## 🤝 Katkıda Bulunma

1. Fork edin
2. Feature branch oluşturun (`git checkout -b feature/amazing-feature`)
3. Commit edin (`git commit -m 'feat: Add amazing feature'`)
4. Push edin (`git push origin feature/amazing-feature`)
5. Pull Request açın

---

## 📧 İletişim

Sorularınız için issue açabilir veya ekip üyeleriyle iletişime geçebilirsiniz.

---

## 🙏 Teşekkürler

- [Meta](https://ai.meta.com/) - Llama foundation models
- **Llama Hackathon** organizatörleri

---

<div align="center">

**⭐ Projeyi beğendiyseniz yıldız vermeyi unutmayın! ⭐**

Made with 🦙 by KodLlama Takımı

</div>
