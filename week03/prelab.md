# SE2009: Data Structures – Week 03 PreLab: Custom Data Types with `struct`, Memory Alignment & Struct Pointers

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Assignment:** PreLab 03 – User-Defined Heterogeneous Data Types, Memory Alignment & Struct Pointers
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi / LMS)**  
  *(Ödev teslimleri doğrudan Üniversite DYS sistemi üzerinden yüklenecektir.)*
* **Submission Deliverable:** Microsoft Word (`.docx`) veya PDF Raporu
* **File Naming Convention:** `studentNo_surname_name_SE2009_PreLab03.docx`
* **Difficulty Level:** Basit / Orta (Kavramsal Analiz, Bellek Düzeni ve Kod Takibi)

> [!IMPORTANT]
> **Ödev Teslim Duyurusu:**  
> Bu hazırlık ödevi (PreLab) laboratuvar öncesi teorik temellerinizi pekiştirmek için tasarlanmıştır. Çözümlerinizi hazırlayıp Microsoft Word veya PDF belgesi formatında **DYS (Ders Yönetim Sistemi)** üzerindeki ilgili ödev alanına yükleyiniz. Belgenizin en üstüne görüntüleme/indirme yetkisi verilmiş güncel OneDrive paylaşım linkinizi eklemeyi unutmayınız.

---

# 🧠 BÖLÜM 1: Özel Veri Yapıları (`struct`) ve Bellek Mimarisi Rehberi

### 1.1. Neden `struct` (Heterojen Veri Paketleme)?

Diziler (arrays) yalnızca **aynı tipteki** (homojen) verileri yan yana saklayabilir (`int sayilar[10]`). Gerçek dünyadaki bir öğrenciyi, sensörü ya da banka hesabını modellemek için farklı tipleri (`int`, `char[]`, `float`, `double`) bir arada tutmaya ihtiyaç duyarız.

C dilinde `struct` (structure), farklı veri tiplerini tek bir mantıksal çatı altında toplayan ve bellekte ardışık olarak saklayan **kullanıcı tanımlı özel bir veri yapısıdır**.

```c
// typedef ile temiz tip tanımlama:
typedef struct {
    int ogrenci_no;      // 4 byte
    char ad[30];         // 30 byte
    float vize_notu;     // 4 byte
} Ogrenci;

Ogrenci ogr1; // 'struct Ogrenci ogr1;' yazma zorunluluğunu ortadan kaldırır
```

---

### 1.2. Bellek Hizalama (Memory Alignment) ve Struct Padding Nedir?

Modern işlemciler (özellikle 32-bit ve 64-bit CPU'lar) ana bellekten veri okurken her seferinde 1 byte okumak yerine 4 veya 8 byte'lık bloklar (Word) halinde okurlar. Bir değişkenin bellek adresi kendi veri tipinin katına denk gelmiyorsa işlemci fazladan okuma döngüsü harcamak zorunda kalır.

Bu performans kaybını engellemek için C derleyicisi struct üyeleri arasına görünmez **boşluk byte'ları (padding)** ekler.

```text
===================================================================================
                  BELLEK HİZALAMA VE PADDING ŞEMASI (64-bit CPU)
===================================================================================
A) Dengesiz Sıralama:
struct KotuHizalama {
    char c;     // 1 byte + 3 byte PADDING (int 4'ün katında başlamalı)
    int sayi;   // 4 byte
    char son;   // 1 byte + 3 byte PADDING (struct toplamı 4'ün katı olmalı)
}; // TOPLAM: 12 BYTE (4 byte veri + 2 byte veri = 6 byte yerine 12 byte!)

B) Optimize Sıralama (Büyük tipler önce):
struct IyiHizalama {
    int sayi;   // 4 byte
    char c;     // 1 byte
    char son;   // 1 byte + 2 byte PADDING
}; // TOPLAM: 8 BYTE
===================================================================================
```

---

### 1.3. Nokta (`.`) Operatörü vs. Ok (`->`) Operatörü

* **Nokta (`.`):** Doğrudan struct nesnesi üzerinden elemana erişilir: `ogr1.vize_notu = 85.0;`
* **Ok (`->`):** Struct'ı gösteren bir **işaretçi (pointer)** üzerinden elemana erişilir.
  $$\text{ptr}\rightarrow\text{alan} \iff (*\text{ptr}).\text{alan}$$
* **Neden İşaretçi ile Aktarırız?**
  Bir fonksiyona struct'ı doğrudan parametre verirseniz (`void goster(Ogrenci ogr)`), struct içindeki tüm baytlar kopyalanır (Stack şişer). İşaretçi ile aktarıldığında (`void goster(Ogrenci *ogr)`), sadece **8 byte**'lık bellek adresi kopyalanır ve işlem katbekat hızlı çalışır.

---

### 1.4. Öz-Yinelemeli Yapılar (Self-Referential Structs): Bağlı Listelerin Temeli

Bir struct'ın kendi tipinden bir **işaretçi** barındırmasına **Öz-Yinelemeli Yapı (Self-Referential Struct)** denir.

```c
typedef struct Dugum {
    int veri;
    struct Dugum *sonraki; // Kendisi gibi bir Düğümün adresini tutar!
} Dugum;
```

> [!NOTE]
> `struct Dugum sonraki;` şeklinde doğrudan kendi nesnesini içeremez (sonsuz bellek özyinelemesi oluşur). Ancak `struct Dugum *sonraki;` yazılabilir; çünkü 64-bit mimaride tüm işaretçilerin boyutu bellidir: tam **8 byte**.

---

# ❓ BÖLÜM 2: PreLab Değerlendirme Soruları

Aşağıdaki 4 soruyu cevaplayıp Word/PDF formatında **DYS** üzerinden teslim ediniz.

---

### Soru 1: Struct Bellek Hizalama (Padding) Hesabı
Aşağıdaki iki struct tanımını inceleyiniz:

```c
struct RaporA {
    char kod;
    double deger;
    char tip;
};

struct RaporB {
    double deger;
    char kod;
    char tip;
};
```

* **İstenenler:**
  1. 64-bit bir sistemde `sizeof(struct RaporA)` ve `sizeof(struct RaporB)` değerleri kaç byte çıkacaktır?
  2. Her iki struct da aynı eleman türlerine sahip olmasına rağmen aradaki boyut farkının nedenini satır satır padding byte'larını göstererek açıklayınız.

---

### Soru 2: Nokta (`.`) vs. Ok (`->`) ve Call-by-Reference Analizi
Bir akıllı saat uygulamasında kullanıcının nabız değerini güncellemek isteyen geliştirici şu fonksiyonu yazmıştır:

```c
#include <stdio.h>

typedef struct {
    int kullanici_id;
    int nabiz;
} SaglikVerisi;

void nabizGuncelleHatali(SaglikVerisi veri, int yeni_nabiz) {
    veri.nabiz = yeni_nabiz;
}

void nabizGuncelleDogru(SaglikVerisi *veri, int yeni_nabiz) {
    // TODO: Ok operatörü ile tamamlayınız
}

int main(void) {
    SaglikVerisi saat1 = {101, 72};

    nabizGuncelleHatali(saat1, 95);
    printf("Hatali sonrasi nabiz: %d\n", saat1.nabiz);

    nabizGuncelleDogru(&saat1, 95);
    printf("Dogru sonrasi nabiz:  %d\n", saat1.nabiz);

    return 0;
}
```

* **İstenenler:**
  1. `nabizGuncelleHatali` çağrıldığında `saat1.nabiz` değerinin neden 72 olarak kaldığını (değişmediğini) Stack bellek kopyalaması açısından açıklayınız.
  2. `nabizGuncelleDogru` fonksiyonunu işaretçi ve ok (`->`) operatörü kullanarak yazınız ve `(*veri).nabiz` ifadesi ile `veri->nabiz` ifadesinin denkliğini açıklayınız.

---

### Soru 3: Dinamik Struct Ayırma (`malloc`) ve Güvenli Bellek İadesi
Heap üzerinde dinamik olarak 1 adet `SaglikVerisi` struct'ı oluşturmak isteyen bir yazılımcı için:
1. `malloc` fonksiyonunu kullanarak Heap'ten gerekli boyutta bellek ayıran C kod satırını yazınız.
2. Bu dinamik bellek alanı kullanıldıktan sonra bellek sızıntısını (Memory Leak) ve boşta kalan işaretçi (Dangling Pointer) hatasını önlemek için hangi iki satır C kodu çalıştırılmalıdır?

---

### Soru 4: Öz-Yinelemeli Yapı (Self-Referential Struct) ve Bağlı Liste Kavramı
Aşağıdaki kod parçasını inceleyiniz:

```c
typedef struct TrenVagonu {
    int vagon_no;
    struct TrenVagonu *sonraki_vagon;
} Vagon;
```

* **İstenenler:**
  1. `struct TrenVagonu` içinde `sonraki_vagon` değişkeni bir işaretçi (`*`) olmak zorundadır. Neden doğrudan `struct TrenVagonu sonraki_vagon;` yazılamaz? Derleyici bu durumda nasıl bir mantıksal/boyutsal çıkmaza girer?
  2. Birbirine bağlanmış 3 vagonluk bir tren yapısını temsil eden bir kutu/ok bellek çizimi yapınız veya ASCII diyagramı ile açıklayınız (Son vagonun nereyi gösterdiğini belirtiniz).

---

## 📄 DYS Teslim Şablonu

Raporunuzu aşağıdaki başlık yapısıyla Word/PDF olarak hazırlayıp **DYS**'ye yükleyiniz:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN ÜNİVERSİTESİ
                             MÜHENDİSLİK FAKÜLTESİ
                          YAZILIM MÜHENDİSLİĞİ BÖLÜMÜ
                       SE2009 - PRELAB 03 DEĞERLENDİRME RAPORU
================================================================================

ÖĞRENCİ BİLGİLERİ:
• Adı Soyadı: [Adınız Soyadınız]
• Öğrenci No: [Öğrenci Numaranız]
• GitHub Kullanıcı Adı: se-[Öğrenci Numaranız]
• Teslim Tarihi: [GG/AA/YYYY]
• OneDrive Bağlantısı: [Erişilebilir OneDrive Linkiniz]

--------------------------------------------------------------------------------
SORU 1: STRUCT PADDING VE BELLEK HİZALAMA
Cevabınız:
(RaporA ve RaporB boyut hesaplamalarını ve padding byte yerleşimini açıklayınız.)


SORU 2: NOKTA (.) VS. OK (->) VE CALL-BY-REFERENCE
Cevabınız:
(Hatalı güncellemenin neden çalışmadığını ve doğru fonksiyon kodunu açıklayınız.)


SORU 3: DİNAMİK STRUCT AYIRMA VE BELLEK TEMİZLİĞİ
Cevabınız:
(malloc tahsisi, free() ve NULL güvenliği kodlarını yazınız.)


SORU 4: ÖZ-YİNELEMELİ YAPILAR VE BAĞLI LİSTE MANTIĞI
Cevabınız:
(Neden işaretçi olmak zorunda olduğunu ve 3 düğümlü bağlantı diyagramını açıklayınız.)

================================================================================
```
