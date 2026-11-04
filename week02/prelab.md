# SE2009: Data Structures – Week 02 PreLab: Pointers, Memory Architecture & Memory Leak Fundamentals

## 📌 Course & Assignment Information

* **Course:** SE2009 (Data Structures)
* **Assignment:** PreLab 02 – Pointer Mechanics, CPU/GPU Memory Management & Memory Leaks
* **Central Announcement Repo:** [https://github.com/se2009-26/assignments](https://github.com/se2009-26/assignments)
* **Submission Platform:** **DYS (Ders Yönetim Sistemi / LMS)**  
  *(Ödev teslimleri GitHub yerine doğrudan Üniversite DYS sistemi üzerinden yüklenecektir.)*
* **Submission Deliverable:** Microsoft Word (`.docx`) veya PDF Raporu
* **File Naming Convention:** `studentNo_surname_name_SE2009_PreLab02.docx`
* **Difficulty Level:** Basit / Orta-Altı (Temel Kavrama ve Kod Takibi)

> [!IMPORTANT]
> **Ödev Teslim Duyurusu:**  
> Bu ödevin yönergeleri GitHub üzerinden duyurulmakta olup, hazırladığınız teslim raporunu **DYS (Ders Yönetim Sistemi)** üzerindeki ilgili ödev alanına yüklemeniz gerekmektedir.

---

# 🧠 BÖLÜM 1: Pointer (İşaretçi) ve Bellek Mimarisi Rehberi

### 1.1. Bellek Nedir? Adres ve Değer Ayrımı

Bilgisayarın ana belleği (RAM), her biri $1\text{ byte}$ ($8\text{ bit}$) genişliğinde olan ve $0\text{x00000000}$'dan başlayarak ardışık olarak numaralandırılmış hücrelerden (kutulardan) oluşur.

```text
+-------------------+--------------------+--------------------+
| Bellek Adresi     | 0x7FFF0000         | 0x7FFF0004         |
+-------------------+--------------------+--------------------+
| Değişken Adı      | int sayi = 42;     | int *ptr = &sayi;  |
| Saklanan Değer    | 42                 | 0x7FFF0000         |
+-------------------+--------------------+--------------------+
```

* **Adres Operatörü (`&`):** Bir değişkenin bellekte nerede durduğunu (adresini) verir. `&sayi` $\rightarrow$ `0x7FFF0000`
* **İçerik/Yönlendirme Operatörü (`*`):** Bir işaretçinin gösterdiği adresteki gerçek veriye erişir veya onu değiştirir. `*ptr = 100;` dendiğinde `sayi` değişkeninin değeri `100` olur.

---

### 1.2. Neden İşaretçinin Tipi Vardır? (`int*`, `char*`, `float*`)

Bütün işaretçiler bellekte sadece bir **adres** saklar (64-bit sistemlerde tüm işaretçiler 8 byte yer kaplar). Fakat işaretçinin tipinin tanımlanması 2 kritik nedenden ötürü zorunludur:
1. **Adresten Kaç Byte Okunacağını Belirlemek:** `*ptr` dediğimizde `char*` ise 1 byte, `int*` ise 4 byte okunur.
2. **İşaretçi Aritmetiği Adım Boyutu (Stride):** `ptr + 1` yaptığımızda:
   * `char *cptr` $\rightarrow$ adres $1\text{ byte}$ artar.
   * `int *iptr` $\rightarrow$ adres $4\text{ byte}$ artar.

---

### 1.3. Dinamik Bellek ve Bellek Sızıntısı (Memory Leak) Nedir?

```c
int *ptr = (int*)malloc(sizeof(int) * 100); // Heap'te 400 byte yer ayrıldı
// ... işlemler yapıldı ...
free(ptr);   // Bellek sisteme iade edildi!
ptr = NULL;  // Dangling Pointer (Boşta Kalan İşaretçi) önlendi!
```

* **Bellek Sızıntısı (Memory Leak):** `malloc()` ile ayrılan bir bellek bloğunun adresi kaybedilirse (örneğin işaretçiye başka bir adres atanırsa veya fonksiyon bittiğinde `free()` çağrılmamışsa), o bellek alanı işletim sistemine geri verilemez. Program çalıştıkça RAM şişer ve sistem kilitlenir.

---

### 1.4. Özel Konu: CPU vs. GPU Bellek Sızıntısı (Host vs. Device Memory)

Modern yapay zeka, grafik ve veri mühendisliği uygulamalarında iki farklı bellek katmanı vardır:

1. **CPU Belleği (Host RAM):** `malloc()` / `free()` ile yönetilir.
2. **GPU Belleği (Device VRAM):** Grafik kartının kendi süper hızlı belleğidir (VRAM). C/CUDA ortamında `cudaMalloc()` ile ayrılır ve `cudaFree()` ile serbest bırakılır.

```text
[ CPU (Host) RAM ]  --- (PCIe Veri Yolu) ---> [ GPU (Device) VRAM ]
malloc() / free()                             cudaMalloc() / cudaFree()
```

* ⚠️ **GPU Memory Leak:** Eğer bir yapay zeka eğitim döngüsünde veya oyun motorunda her karede (frame) GPU üzerinde dinamik bellek ayrılıp `cudaFree()` ile temizlenmezse, GPU belleği birkaç saniye içinde dolar ve meşhur **"CUDA Out of Memory (OOM)"** hatası oluşarak program çöker. Pointers, hem CPU hem de GPU belleğini yöneten temel köprüdür.

---

# ❓ BÖLÜM 2: PreLab Değerlendirme Soruları

Aşağıdaki 4 basit/orta-altı soruyu Microsoft Word veya PDF belgesinde cevaplayıp **DYS** üzerinden teslim ediniz.

---

### Soru 1: Temel Pointer Mekaniği ve Kod Çıktısı Takibi
Aşağıdaki C kod parçası satır satır çalıştırıldığında bellekteki değerlerin nasıl değiştiğini açıklayınız ve `printf` satırlarının ekran çıktılarını yazınız:

```c
#include <stdio.h>

int main() {
    int x = 10;
    int y = 20;
    int *p1 = &x;
    int *p2 = &y;

    *p1 = *p1 + 5;
    *p2 = *p1 * 2;
    p1 = p2;
    *p1 = 50;

    printf("x = %d\n", x);
    printf("y = %d\n", y);
    printf("*p1 = %d\n", *p1);
    printf("*p2 = %d\n", *p2);

    return 0;
}
```
* **İstenenler:**
  1. Kodun konsola basacağı 4 çıktıyı yazınız.
  2. `p1 = p2;` satırından sonra `*p1 = 50;` yapıldığında `x` mi yoksa `y` değişkeni mi değişmiştir? Nedenini kısaca açıklayınız.

---

### Soru 2: Bellek Sızıntısı (Memory Leak) ve CPU/GPU Karşılaştırması
1. Bir programda `malloc()` ile ayrılmış bir bellek bloğunun `free()` edilmemesi durumunda ortaya çıkan **Bellek Sızıntısı (Memory Leak)** nedir? Kendi cümlelerinizle basit bir örnek vererek açıklayınız.
2. GPU (Ekran Kartı) belleğinde meydana gelen bir bellek sızıntısı ile CPU (Ana RAM) sızıntısını karşılaştırınız. Özellikle derin öğrenme (Deep Learning) modelleri eğitilirken GPU VRAM sızıntısının programa etkisi ne olur?

---

### Soru 3: Dizi ve İşaretçi Aritmetiği (Pointer Arithmetic)
Aşağıdaki C kodunu inceleyiniz:

```c
#include <stdio.h>

int main() {
    int dizi[4] = {100, 200, 300, 400};
    int *ptr = dizi; // dizi'nin başlangıç adresini tutar

    printf("1. Eleman: %d\n", *ptr);
    ptr++;
    printf("2. Eleman: %d\n", *ptr);
    printf("4. Eleman: %d\n", *(ptr + 2));

    return 0;
}
```
* **İstenenler:**
  1. `ptr++` yapıldığında bellekteki adres değeri neden 1 byte değil de 4 byte (veya `sizeof(int)`) kadar artmaktadır?
  2. `*(ptr + 2)` ifadesi hangi dizi elemanını ekrana basar?

---

### Soru 4: Fonksiyonlarda Değer ile Çağırma (Call by Value) vs. İşaretçi ile Çağırma (Call by Reference)
İki tam sayının değerini birbiriyle takas etmek (swap) isteyen bir öğrenci şu fonksiyonu yazmıştır:

```c
void hataliSwap(int a, int b) {
    int gecici = a;
    a = b;
    b = gecici;
}
```
* **İstenenler:**
  1. `main()` içerisinden `hataliSwap(x, y)` çağrıldığında `x` ve `y` değerlerinin neden **değişmediğini** açıklayınız.
  2. Bu fonksiyonun `x` ve `y` değerlerini gerçekten değiştirebilmesi için işaretçi (pointer) kullanarak fonksiyonu doğru şekilde yeniden yazınız (`void dogruSwap(...)`).

---

## 📄 DYS Teslim Şablonu

Raporunuzu aşağıdaki başlık yapısıyla Word/PDF olarak hazırlayıp **DYS**'ye yükleyiniz:

```text
================================================================================
                        MUĞLA SITKI KOÇMAN ÜNİVERSİTESİ
                             MÜHENDİSLİK FAKÜLTESİ
                         YAZILIM MÜHENDİSLİĞİ BÖLÜMÜ
                      SE2009 - PRELAB 02 DEĞERLENDİRME RAPORU
================================================================================

ÖĞRENCİ BİLGİLERİ:
• Adı Soyadı: [Adınız Soyadınız]
• Öğrenci No: [Öğrenci Numaranız]
• GitHub Kullanıcı Adı: se-[Öğrenci Numaranız]
• Teslim Tarihi: [GG/AA/YYYY]

--------------------------------------------------------------------------------
SORU 1: TEMEL POINTER MEKANİĞİ VE KOD ÇIKTISI TAKİBİ
Cevabınız:
(Kodun ekran çıktılarını ve p1=p2 atamasının mantığını açıklayınız.)


SORU 2: BELLEK SIZINTISI (MEMORY LEAK) VE CPU/GPU ETKİLERİ
Cevabınız:
(Memory leak tanımı, free() önemi ve GPU VRAM sızıntısının etkisini yazınız.)


SORU 3: DİZİ VE İŞARETÇİ ARİTMETİĞİ
Cevabınız:
(Adres adımlama boyutu ve *(ptr+2) mantığını açıklayınız.)


SORU 4: CALL BY VALUE VS. CALL BY REFERENCE (SWAP FONKSİYONU)
Cevabınız:
(Hatalı swap neden çalışmaz ve doğru pointer tabanlı swap fonksiyonu.)

================================================================================
```
