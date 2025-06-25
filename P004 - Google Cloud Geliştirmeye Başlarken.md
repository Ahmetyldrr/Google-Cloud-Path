# Module 2: Google Cloud Geliştirmeye Başlarken

Google Cloud, uygulamalarınızı oluştururken faydalanabileceğiniz çeşitli platformlar sunar. Bu modülde; Cloud API’lerine, Google Cloud SDK’ya, CLI araçlarına, Cloud Client Libraries’e, Cloud Shell ve Cloud Code’a ve Cloud Workstations’a genel bir bakış yapacağız.

## 1. Cloud API’leri ve Google Cloud SDK
- **Cloud API**: Compute Engine, Cloud Storage, BigQuery, AI gibi Google Cloud servislerine programatik erişim sağlar.  
  - Çağrılar: HTTP/JSON veya gRPC (daha hızlı ve sıkıştırılmış ikili protokol)  
  - Kimlik doğrulama: Service Account anahtarı veya ADC (Application Default Credentials)
- **Google Cloud SDK**:  
  - Komut satırı araçları (`gcloud`, `gsutil`, `bq` vb.)  
  - Dil-spesifik Cloud Client Libraries (Python, Java, Go…)

## 2. Google Cloud CLI Araçları
- **gcloud CLI**  
  - Bulut kaynaklarını yönetir: `gcloud compute instances list`, `gcloud projects describe` vb.  
  - Bileşen yönetimi:  
    ```bash
    gcloud components list
    gcloud components install kubectl
    gcloud components update
    ```
- **gsutil / gcloud storage**  
  - Cloud Storage için: bucket ve obje işlemleri  
  - `gcloud storage` komutları, `gsutil`’e göre daha hızlı ve tek tip arayüz sunar.  
- **bq**  
  - BigQuery veri ambarı yönetimi ve sorgulama  
  - Örnek:  
    ```bash
    bq query --use_legacy_sql=false \
      'SELECT word, COUNT(*) FROM `bigquery-public-data.samples.shakespeare` GROUP BY word;'
    ```

## 3. Cloud Client Libraries
- Direct API çağrıları yerine tercih edilen yöntem  
- Otomatik kimlik doğrulama, retry mantığı, gRPC performansı  
- Desteklenen diller: Python, Node.js, Java, Go, PHP, Ruby, C++, .NET  
- **Örnek (Python Cloud Storage)**  
  ```python
  from google.cloud import storage

  # Varsayılan kimlik bilgileri (ADC) ile client oluşturma
  client = storage.Client()
  bucket = client.create_bucket("my-new-bucket")
  print(f"Bucket {bucket.name} oluşturuldu.")
  ```

## 4. SDK Kurulumu ve Başlatma
### 4.1. İndirme ve Yükleme
- **Linux/macOS**  
  ```bash
  curl https://sdk.cloud.google.com | bash
  ```
- **Windows**  
  - MSI yükleyiciyi indirip çalıştırın:  
    https://cloud.google.com/sdk/docs/install#windows

### 4.2. `gcloud init`
```bash
gcloud init
```
1. Google hesabınızla oturum açma  
2. Mevcut projeyi seçme veya yeni proje oluşturma  
3. Varsayılan bölge (region) ve zon (zone) belirleme

### 4.3. Autocomplete ve Etkileşimli Kabuk
```bash
source "$(dirname $(which gcloud))/../completion.bash.inc"
gcloud alpha interactive
```

## 5. Cloud Shell
- **Tarayıcı tabanlı Debian VM**  
  - 5 GB kalıcı disk, 1 saat boşta kalınca durur, disk korunur  
- **Hazır SDK & Yetkilendirme**: `gcloud`, `kubectl`, `docker` vb.  
- **Theia Tabanlı editör**: IntelliSense, Git desteği

## 6. Cloud Code
- **IDE Eklentileri**: VS Code, IntelliJ IDEA, PyCharm, Cloud Shell Editor  
- **Geliştirme Yardımcıları**  
  - Secret Manager entegrasyonu ve Cloud API gezinme  
- **Kubernetes Explorer**: Pod, Service, ConfigMap görsel yönetimi; YAML autocomplete  
- **Cloud Run**: Local emulator, canlı servis yönetimi

## 7. Yerel Emülatörler
- Desteklenen servisler: Bigtable, Datastore, Firestore, Pub/Sub, Spanner  
```bash
gcloud beta emulators start firestore
gcloud beta emulators start pubsub
eval $(gcloud beta emulators pubsub env-init)
```
- Ortam değişkenleri ile gerçek servise dokunmadan test

## 8. Cloud Workstations
- **Yönetilen ve tekrarlanabilir geliştirme ortamları**  
- Ephemeral Compute Engine VM’leri (müşterinin VPC’si içinde)  
- Tarayıcı, SSH veya IDE üzerinden erişim  
- Otomatik durdurma/başlatma ile maliyet optimizasyonu  

