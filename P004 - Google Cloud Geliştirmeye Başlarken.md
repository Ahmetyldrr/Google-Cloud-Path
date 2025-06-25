# Module 2: Google Cloud Geliştirmeye Başlarken (Detaylı)

Bu modülde, Google Cloud ekosistemindeki temel geliştirme araçları ve hizmetleri ayrıntılı olarak inceleyecek, her bileşeni nasıl kullanacağınıza dair örnekler ekleyeceğiz.

---

## 1. Cloud API’leri ve Google Cloud SDK

### 1.1. Cloud API Nedir?
Google Cloud API’leri, Compute Engine, Cloud Storage, BigQuery, AI Platform gibi servisleri programatik olarak kullanmanızı sağlar.

- **Protokoller**  
  - HTTP/JSON: Standart REST çağrıları  
  - gRPC: İkili protokol, yüksek performanslı ve düşük gecikmeli

### 1.2. Kimlik Doğrulama
- **Service Account** anahtar dosyası (`.json`)  
- **Application Default Credentials (ADC)**  
  ```bash
  # Service account anahtarını kullanarak ADC ayarlama
  export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"
  ```

### 1.3. Örnek: Compute Engine API Çağrısı
```bash
curl -X GET   -H "Authorization: Bearer $(gcloud auth print-access-token)"   -H "Accept: application/json"   "https://compute.googleapis.com/compute/v1/projects/PROJECT_ID/zones/us-central1-a/instances"
```

---

## 2. Google Cloud CLI Araçları

### 2.1. gcloud CLI
- **Genel kullanım**:  
  ```bash
  # Proje ayarlama
  gcloud config set project my-project
  # VM örneklerini listeleme
  gcloud compute instances list
  ```
- **Varsayılan bölge ve zon**:  
  ```bash
  gcloud config set compute/region europe-west1
  gcloud config set compute/zone europe-west1-b
  ```
- **Bileşen yönetimi**:  
  ```bash
  gcloud components list
  gcloud components install kubectl
  gcloud components update
  ```

### 2.2. gsutil / gcloud storage
- **Bucket oluşturma**:  
  ```bash
  gsutil mb -l europe-west1 gs://my-bucket/
  ```
- **Obje kopyalama**:  
  ```bash
  gsutil cp localfile.txt gs://my-bucket/path/
  ```
- **gcloud storage muadili**:  
  ```bash
  gcloud storage buckets create my-bucket --location=europe-west1
  gcloud storage objects copy localfile.txt gs://my-bucket/path/
  ```

### 2.3. bq (BigQuery CLI)
- **Dataset oluşturma**:  
  ```bash
  bq mk --dataset my-project:my_dataset
  ```
- **Tablo oluşturma**:  
  ```bash
  bq mk --table my-project:my_dataset.my_table schema.json
  ```
- **Sorgu çalıştırma**:  
  ```bash
  bq query --use_legacy_sql=false     'SELECT word, COUNT(*) AS count
     FROM `bigquery-public-data.samples.shakespeare`
     GROUP BY word
     ORDER BY count DESC
     LIMIT 10;'
  ```

---

## 3. Cloud Client Libraries

### 3.1. Avantajlar
- Otomatik kimlik doğrulama (ADC kullanımı)
- Retry mantığı ve zaman aşımı yönetimi
- gRPC ile performans artışı

### 3.2. Örnek: Python ile Cloud Storage
```python
from google.cloud import storage

# Varsayılan kimlik bilgileri (ADC) kullanılarak client oluşturma
client = storage.Client()
bucket_name = "my-new-bucket"

# Bucket oluşturma
bucket = client.create_bucket(bucket_name)
print(f"Bucket oluşturuldu: {bucket.name}")
```

### 3.3. Örnek: Node.js ile BigQuery
```javascript
const {BigQuery} = require('@google-cloud/bigquery');
const bigquery = new BigQuery();

async function simpleQuery() {
  const query = 'SELECT name, COUNT(*) as count FROM `bigquery-public-data.usa_names.usa_1910_2013` GROUP BY name ORDER BY count DESC LIMIT 5;';
  const [rows] = await bigquery.query(query);
  console.log('En popüler ilk 5 isim:');
  rows.forEach(row => console.log(row.name, row.count));
}
simpleQuery();
```

---

## 4. SDK Kurulumu ve Başlatma

### 4.1. İndirme ve Yükleme
- **Linux/macOS**  
  ```bash
  curl https://sdk.cloud.google.com | bash
  exec -l $SHELL
  ```
- **Windows (MSI)**  
  1. https://cloud.google.com/sdk/docs/install#windows adresinden indirin  
  2. Setup’ı çalıştırın ve ortam değişkenlerini kontrol edin

### 4.2. `gcloud init`
```bash
gcloud init
```
- Adımlar:  
  1. Google hesabıyla oturum açın  
  2. Mevcut projenizi seçin veya yeni proje oluşturun  
  3. Varsayılan bölge ve zon bilgisi girin

### 4.3. Autocomplete ve Etkileşimli Kabuk
- Autocomplete komutu:
  ```bash
  source "$(dirname $(which gcloud))/../completion.bash.inc"
  ```
- Etkileşimli mod:
  ```bash
  gcloud alpha interactive
  ```

---

## 5. Cloud Shell

- **Ana Özellikler**  
  - 5 GB kalıcı disk  
  - Otomatik SDK yüklemesi ve yetkilendirme  
  - Debian tabanlı tarayıcı içi VM  
- **Kullanım Örneği**  
  1. Google Cloud Console’da sağ üstten “Activate Cloud Shell” tıklayın  
  2. Terminalde `gcloud projects list` komutunu çalıştırın  
- **Theia Kod Editörü**  
  - `code .` komutuyla editörü açın  
  - IntelliSense ve yerleşik Git desteği kullanın

---

## 6. Cloud Code

- **Kurulum ve Başlatma**  
  - VS Code: Market’ten “Cloud Code” eklentisini yükleyin  
  - JetBrains IDE: Plugins → “Cloud Code”  
- **Örnek: Cloud Run’da Deploy**  
  1. Projenizi açın  
  2. Sağ tıklayarak “Cloud Code: Deploy to Cloud Run” seçin  
  3. Hizmet adını ve bölgeyi girin  
- **Kubernetes Geliştirme**  
  - Kubernetes Explorer’dan Pod loglarını izleyin  
  - YAML dosyasına schema autocomplete ile hatasız yapı ekleyin  

---

## 7. Yerel Emülatörler

- **Servis Başlatma**  
  ```bash
  gcloud beta emulators firestore start --host-port=localhost:8080
  gcloud beta emulators pubsub start --project=my-project
  ```
- **Ortam Değişkenleri**  
  ```bash
  $(gcloud beta emulators firestore env-init)
  $(gcloud beta emulators pubsub env-init)
  ```
- **Uygulama Örneği**  
  ```python
  from google.cloud import pubsub_v1
  import os
  # Emulator’a bağlanmak için
  os.environ["PUBSUB_EMULATOR_HOST"] = "localhost:8085"

  publisher = pubsub_v1.PublisherClient()
  topic_path = publisher.topic_path("my-project", "test-topic")
  publisher.create_topic(request={"name": topic_path})
  print(f"Topic oluşturuldu: {topic_path}")
  ```

---

## 8. Cloud Workstations

- **Yapılandırma Örneği (workstations.yaml)**  
  ```yaml
  apiVersion: workstations.cloud.google.com/v1alpha1
  kind: WorkstationConfig
  metadata:
    name: dev-workstation
  spec:
    machine:
      type: e2-standard-4
    container:
      image: gcr.io/my-project/dev-image:latest
    persistentDisk:
      sizeGb: 50
  ```
- **Başlatma**  
  ```bash
  gcloud workstations create dev-ws --config=workstations.yaml
  gcloud workstations start dev-ws
  gcloud workstations connect dev-ws
  ```
- **Özellikler**  
  - Tekrarlanabilir ortamlar: Tüm bağımlılıklar container içinde  
  - Ephemeral VM: Kullanım sonrası durdurulur  
  - Müşteri VPC’sinde güvenlik ve erişim kontrolü  

---

### İleri Adımlar
Bu bileşenlerle temel geliştime ortamınızı kurduktan sonra:
1. Basit bir “Hello World” Cloud Function deploy edin.  
2. Cloud Shell’den bir Kubernetes kümesine bağlanarak bir nginx Pod’u başlatın.  
3. Local emülatörlerde Firestore kullanarak veri modelinizi test edin.  

Bu örnekler, Google Cloud’un araç setini deneyimlemeniz ve bir sonraki seviyede uygulama geliştirme süreçlerinizi otomatikleştirmeniz için ilk adımlardır.  


