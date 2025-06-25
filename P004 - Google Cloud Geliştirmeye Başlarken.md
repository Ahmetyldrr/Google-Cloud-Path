# Module 2: Google Cloud Geliştirmeye Başlarken

Google Cloud uygulamalarınızı oluşturmak ve yönetmek için ihtiyaç duyacağınız temel araçlara ve hizmetlere giriş.

## 1. Cloud APIs ve Google Cloud SDK  
- **Cloud APIs**: Compute, networking, storage, ML gibi servislere programatik erişim sağlar.  
  - HTTP/JSON veya gRPC ile çağrı  
  - Kimlik bilgileri (service account) ile yetkilendirme  
- **Google Cloud SDK**:  
  - CLI araçları (gcloud, gsutil, bq)  
  - Dil-spesifik Cloud Client Libraries  

## 2. Google Cloud CLI Araçları  
- **gcloud CLI**:  
  - Kaynak yönetimi (ör. `gcloud compute instances list`)  
  - Bileşen yönetimi (`gcloud components list/install/update`)  
- **gsutil / gcloud storage**:  
  - Cloud Storage bucket ve obje işlemleri  
  - Tercihen `gcloud storage` (daha hızlı, gcloud uyumlu)  
- **bq**:  
  - BigQuery veri ambarı yönetimi ve sorgulama  

## 3. Cloud Client Libraries  
- Doğal dil konvansiyonlarına uygun, kimlik doğrulama ve retry desteği hazır kütüphaneler  
- Python, Node.js, Java, Go, PHP, Ruby, C++, .NET desteği  
- Örnek (Python):  
  ```python
  from google.cloud import storage
  client = storage.Client()  
  bucket = client.create_bucket("my-new-bucket")
