# Module 3: Veri Depolama Seçeneklerine Genel Bakış

Bu modülde, uygulamanızın farklı ihtiyaçları için uygun Google Cloud veri depolama servislerini ayrıntılı olarak inceleyeceğiz. Hangi senaryoda hangi servisi seçmeniz gerektiğine dair rehber ve örnek komutlarla desteklenmiş açıklamalar yer almaktadır.

---

## 1. Cloud Storage

**Use Case:** Büyük boyutlu nesne (object) depolama, statik web barındırma, medya dosyaları, yedekleme ve arşivleme.

- **Özellikler:**  
  - Nesneler HTTP üzerinden erişilir (ranged GET ile parça indirme).  
  - Her obje 5 TB’a kadar olabilir.  
  - %99.95 SLA, yüksek dayanıklılık ve tutarlılık.

- **Örnek: Bucket Oluşturma**  
  ```bash
  # 1. Proje yapılandırmasını kontrol et
  gcloud config list
  # 2. Yeni bir bucket oluştur
  gcloud storage buckets create gs://my-image-bucket --location=europe-west1
  ```

- **Örnek: Dosya Yükleme**  
  ```bash
  gcloud storage cp ./local-photo.jpg gs://my-image-bucket/photos/
  ```

---

## 2. Firestore (NoSQL Doküman Veritabanı)

**Use Case:** Mobil ve web uygulamaları, gerçek zamanlı veri güncellemeleri, hiyerarşik veri modelleri.

- **Özellikler:**  
  - Serverless, otomatik ölçeklenir.  
  - Gerçek zamanlı güncellemeler ve offline destek.  
  - Koleksiyonlar → Dokümanlar → Alt koleksiyonlar.

- **Örnek: Firestore Koleksiyon ve Doküman Ekleme (gcloud CLI)**  
  ```bash
  # Koleksiyon adı: users, Doküman ID: user_001
  gcloud firestore documents create projects/$(gcloud config get-value project)/databases/(default)/documents/users/user_001     --data='{"name":"Ahmet","email":"ahmet@example.com","age":30}'
  ```

- **Örnek: Gerçek Zamanlı Güncellemeleri Yakalama (Node.js)**  
  ```javascript
  const admin = require('firebase-admin');
  admin.initializeApp();
  const db = admin.firestore();

  db.collection('users').doc('user_001')
    .onSnapshot(doc => {
      console.log('Güncel Veri:', doc.data());
    });
  ```

---

## 3. Bigtable (NoSQL, Yüksek Performans)

**Use Case:** Tek anahtarlı (single-key) büyük veri, zaman serisi, davranışsal veri, analitik iş yükleri.

- **Özellikler:**  
  - Milyarlarca satır, binlerce kolon.  
  - Alt-10ms tutarlı gecikme.  
  - HBase API uyumu.

- **Örnek: Bigtable Instance Oluşturma**  
  ```bash
  gcloud bigtable instances create my-instance     --cluster=my-cluster --cluster-zone=europe-west1-b --display-name="My Bigtable"
  ```

- **Örnek: Tablo ve Aile (Column Family) Eklemek (cbt CLI)**  
  ```bash
  # Önce cbt CLI kurulu olmalı: gcloud components install cbt
  echo "project = $(gcloud config get-value project)" > ~/.cbtrc
  echo "instance = my-instance" >> ~/.cbtrc

  cbt createtable my-table
  cbt createfamily my-table cf1
  ```

---

## 4. Cloud SQL (İlişkisel Veritabanı)

**Use Case:** Geleneksel MySQL, PostgreSQL veya SQL Server uygulamaları, OLTP iş yükleri, web framework uygulamaları.

- **Özellikler:**  
  - Otomatik yedekleme, replikasyon, failover.  
  - Cloud SQL Auth Proxy ile güvenli bağlantı.

- **Örnek: Cloud SQL MySQL Instance Oluşturma**  
  ```bash
  gcloud sql instances create my-sql-instance     --database-version=MYSQL_8_0 --tier=db-f1-micro --region=europe-west1
  ```

- **Örnek: Auth Proxy Kullanarak Bağlanma**  
  ```bash
  wget https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64 -O cloud_sql_proxy
  chmod +x cloud_sql_proxy

  ./cloud_sql_proxy -instances=$(gcloud config get-value project):europe-west1:my-sql-instance=tcp:3306     -credential_file=/path/to/key.json
  ```

---

## 5. AlloyDB (Yüksek Performanslı PostgreSQL)

**Use Case:** PostgreSQL uyumluluğu, hem OLTP hem OLAP iş yükleri, yüksek ölçeklenebilirlik.

- **Özellikler:**  
  - Compute ve depolama ayrımı, otomatik ölçeklenme.  
  - Standart PostgreSQL’e göre 4× hızlı OLTP, 100× hızlı OLAP.

- **Örnek: AlloyDB Cluster Oluşturma**  
  ```bash
  gcloud alloydb clusters create my-cluster     --region=europe-west1 --network=default --instance-count=1
  ```

---

## 6. Spanner (Global, Dağıtık RDBMS)

**Use Case:** Yüksek kullanılabilirlik, güçlü tutarlılık, küresel dağıtım, kritik işlem veritabanları.

- **Özellikler:**  
  - 99.999% SLA, multi-region replikasyon.  
  - ACID garantisi, SQL desteği.

- **Örnek: Spanner Instance ve Veritabanı Oluşturma**  
  ```bash
  gcloud spanner instances create my-instance     --config=regional-europe-west1 --nodes=3 --description="My Spanner"
  gcloud spanner databases create my-database --instance=my-instance
  ```

---

## Hangi Sayfada Hangi Sorun Çözülür?

| Servis         | Sayfa No | Sorun / Senaryo                                 |
|----------------|----------|--------------------------------------------------|
| Cloud Storage  | Sayfa 5  | Statik içerik barındırma, medya dosyaları        |
| Firestore      | Sayfa 12 | Gerçek zamanlı veri güncellemeleri, offline mod   |
| Bigtable       | Sayfa 19 | Zaman serisi veri, analitik MapReduce iş yükleri |
| Cloud SQL      | Sayfa 27 | Geleneksel ilişkisel veritabanı ihtiyaçları      |
| AlloyDB        | Sayfa 35 | Yüksek performanslı OLTP + OLAP karışımı         |
| Spanner        | Sayfa 42 | Küresel dağıtım, kritik çalışma sürekliliği      |
