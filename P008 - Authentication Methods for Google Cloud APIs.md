# Authentication Methods for Google Cloud APIs

Google Cloud API’lerine uygulamalardan erişirken kullanabileceğiniz farklı kimlik doğrulama yöntemleri. Aşağıdaki akış ve örneklerle hangi yöntemi ne zaman seçmeniz gerektiğini öğrenebilirsiniz.

---

## 1. Uygulama Google Cloud Üzeründe mi Çalışıyor?

### 1.1. Lokal Geliştirme Ortamı
- **Komut:**  
  ```bash
  gcloud auth application-default login
  ```  
- **Kullanım:** Kodunuz `GOOGLE_APPLICATION_CREDENTIALS` veya ADC bulamıyorsa, local kullanıcı kimliğinizi kullanır.  
- **Konum:** `~/.config/gcloud/application_default_credentials.json`

### 1.2. Compute Engine, Cloud Run, Cloud Functions
- **Yöntem:** Kaynağa doğrudan servis hesabı (Service Account) ekleyin.  
- **Adımlar:**  
  1. Özel bir servis hesabı oluşturun ve gerekli IAM rolleri verin.  
     ```bash
     gcloud iam service-accounts create my-sa        --display-name="App SA"
     gcloud projects add-iam-policy-binding my-project        --member="serviceAccount:my-sa@my-project.iam.gserviceaccount.com"        --role="roles/storage.objectViewer"
     ```  
  2. VM veya Cloud Run servisine bu servis hesabını atayın.  
     ```bash
     # Compute Engine VM'e atama
     gcloud compute instances set-service-account my-vm        --service-account=my-sa@my-project.iam.gserviceaccount.com

     # Cloud Run servisine atama
     gcloud run deploy my-service --image gcr.io/my-project/my-image        --service-account my-sa@my-project.iam.gserviceaccount.com
     ```

---

## 2. Google Kubernetes Engine (GKE)

- **Yöntem:** Workload Identity  
- **Avantaj:** K8s Service Account → IAM Service Account eşleştirmesi  
- **Adımlar:**  
  1. Workload Identity etkinleştirme:  
     ```bash
     gcloud container clusters create my-cluster        --enable-workload-identity
     ```  
  2. Eşleme tanımlama:  
     ```bash
     kubectl create serviceaccount ksa
     gcloud iam service-accounts add-iam-policy-binding        --role roles/iam.workloadIdentityUser        --member "serviceAccount:my-project.svc.id.goog[default/ksa]"        ksa@my-project.iam.gserviceaccount.com
     ```  
  3. Pod spec’e `serviceAccountName: ksa` ekleyin.

---

## 3. Bulut Dışı Ortamlar

### 3.1. Workload Identity Federation
- **Şart:** Dış sağlayıcı OpenID Connect ID token oluşturabilmeli.  
- **Yarar:** Service account key gereksinimi ortadan kalkar.  
- **İşlem:** Dış token → Google Access Token dönüşümü

### 3.2. Servis Hesabı Anahtarı (Son Çare)
- **Riskler:** Credential leakage, privilege escalation, identity masking  
- **Öneri:**  
  - Kendi anahtar çiftinizi oluşturun ve yalnızca public key’i yükleyin.  
  - Anahtarları kaynak kodda gömmeyin ve düzenli olarak rotasyon yapın.  
  - **Örnek key yükleme:**  
    ```bash
    gcloud iam service-accounts keys create key.json       --iam-account=my-sa@my-project.iam.gserviceaccount.com       --key-file-type=json
    ```

---

## 4. Application Default Credentials (ADC) Arama Sırası

1. `GOOGLE_APPLICATION_CREDENTIALS` çevre değişkeni  
2. Local user credentials (gcloud auth application-default login)  
3. Eklenmiş servis hesabı  
4. Varsayılan servis hesabı (Compute/GKE/Run)

> Hiçbir credential bulunamazsa hata oluşur.

---

## 5. Örnek: Python ile ADC Kullanımı

```python
from google.cloud import storage

def list_buckets():
    # ADC otomatik kimliği bulur
    client = storage.Client()
    for bucket in client.list_buckets():
        print(bucket.name)

if __name__ == "__main__":
    list_buckets()
```

Kodunuz hem local geliştirme ortamında hem de Google Cloud’da kesintisiz çalışacaktır.
