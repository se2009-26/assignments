# SE2009: Data Structures – Lab 05 Ek Ödev: İleri Düzey Bağlı Liste Algoritmaları & Gösterici Mekaniği

## 📌 Ders ve Ödev Bilgileri

* **Ders:** SE2009 – Veri Yapıları (Data Structures)
* **Ödev Modülü:** Lab 05 Ek Değerlendirme / Pekiştirme Ödevi (Supplementary Homework)
* **Ön Koşul Konuları:** Tek Yönlü Bağlı Liste (Singly Linked List), Bellek Yerleşimi, Dinamik Bellek Yönetimi (`malloc`/`free`), Çift Göstericiler (`Node**`), Hızlı/Yavaş İşaretçi (Runner Technique).
* **Teslim Formatı:** Word (`.docx`) veya PDF raporu (DYS - Ders Yönetim Sistemi üzerinden).
* **Dosya İsimlendirme:** `ogrenciNo_soyad_ad_SE2009_Lab05_EkOdev.docx`

---

# 🧠 BÖLÜM 1: Genel Bilgiler, Bellek Modelleri ve Algoritmik Prensipler

Bu ek ödevde yer alan üç görev, tek yönlü bağlı listelerin (Singly Linked List) dizilerden (Array) ayrışan en temel algoritmik güçlerini ve zayıflıklarını ortaya koymak üzere seçilmiştir:

```text
===================================================================================
                İLERİ DÜZEY BAĞLI LİSTE ALGORİTMALARI KAVRAMSAL MODELİ
===================================================================================

[ 1. İKİ LİSTEYİ BİRLEŞTİRME (CONCATENATION) ]
List 1: [A] -> [B] -> [C] -> NULL
List 2: [D] -> [E] -> [F] -> NULL
İşlem:  List 1'in son düğümünün next göstericisini List 2'nin başına bağla!
Bellek: Sıfır yeni tahsis! (In-Place Pointer Rewiring) ==> O(1) Ek Bellek, O(N1) Zaman.
Dizide: N1 + N2 boyutunda yeni dizi aç, tümünü kopyala ==> O(N1 + N2) Zaman & Bellek.

[ 2. RUNNER (ÇİFT İŞARETÇİ) TEKNİĞİ İLE SONDAN N. DÜĞÜMÜ BULMA ]
Head -> [10] -> [20] -> [30] -> [40] -> [50] -> [60] -> [70] -> NULL
         ^               ^
       slow            fast  (fast tam N adım önden başlatılır)
Adım adım ikisi birden ilerletilir. fast == NULL olduğunda slow tam sondan N. düğümdedir!
Avantaj: Listenin boyutunu önceden saymadan TEK GEÇİŞTE (Single-Pass) O(N) sürede bulur!

[ 3. PALİNDROM / SİMETRİ DOĞRULAMA (İLERİ-YÖNLÜ KISITLAMA) ]
Head -> [1] -> [2] -> [3] -> [2] -> [1] -> NULL
Zorluk:  Tek yönlü bağlı listede 'prev' göstericisi yoktur; geriye yürünemez!
Naif:    Her adımda baştan sayarak front ve rear'a gitmek ==> O(N^2) çok yavaş.
Optimum: Call-Stack (Özyineleme) veya listenin 2. yarısını ters çevirme ==> O(N) Zaman.
===================================================================================
```

---

# 💻 BÖLÜM 2: Ek Ödev Görevleri ve Sorular

---

### 📌 Görev 1: İki Listenin Bellekte Yerinde Birleştirilmesi (In-Place Concatenation)
*Kaynak Örnek: `linked_list_Example_Concatenate.c`*

#### Genel Bilgi:
İki diziyi birleştirmek istediğimizde, dizilerin ardışık bellek blokları olması zorunluluğu nedeniyle her iki dizinin elemanlarını içine alabilecek yeni bir dinamik bellek bloğu (`malloc((size1 + size2) * sizeof(T))`) açılmalı ve tüm elemanlar kopyalanmalıdır. Bağlı listelerde ise elemanlar Heap üzerinde dağınık adreslerde durduğu için, birinci listenin son düğümünün `next` göstericisini ikinci listenin ilk düğümüne yönlendirmek yeterlidir.

#### Sorular:
1. **Bellek ve Karmaşıklık Analizi:**
   * Neden bağlı listelerde iki listeyi birleştirirken yeni bir bellek tahsisine (`malloc`) veya eleman kopyalamasına ihtiyaç duyulmaz?
   * Bu işlemin Zaman Karmaşıklığı (Time Complexity) ve Ek Alan Karmaşıklığı (Auxiliary Space Complexity) nedir? Dizi birleştirme ile kıyaslayınız.
2. **Sınır Durumları (Edge Cases):**
   * Liste 1'in boş olması (`list1 == NULL`) durumunda fonksiyon nasıl davranmalıdır?
   * Liste 2'nin boş olması durumunda ne yapılmalıdır?
   * Fonksiyon başlığını `void concatenate(Node **list1, Node *list2)` şeklinde çift gösterici alacak biçimde tasarlayınız; neden `Node **list1` kullanılması tek göstericiye (`Node *list1`) göre daha güvenlidir?
3. **Uygulama:**
   * `A -> B -> C` listesi ile `D -> E -> F` listesini birleştiren, ardından tüm listeyi bellek sızıntısız temizleyen (`freeList`) tam ve derlenebilir C kodunu yazınız ve terminal çıktısını ekleyiniz.

---

### 📌 Görev 2: Çift İşaretçi (Runner Technique) ile Sondan $N$. Düğümü Bulma
*Kaynak Örnek: `Linked_List_Example_20_Points_to_Nth_Node.c`*

#### Genel Bilgi:
Bir dizide sondan $N$. elemana erişmek `arr[length - n]` formülüyle $O(1)$ sürede doğrudan yapılabilir. Ancak tek yönlü bağlı listede geriye dönük gösterici bulunmadığından ve liste uzunluğu önceden bilinmediğinden, naif yaklaşım iki geçiş gerektirir (1. geçişte eleman sayısı $L$ sayılır, 2. geçişte $L - n$ adım yürünür). İki işaretçi (Fast / Slow Runner) tekniğinde ise tek bir geçişte (Single-Pass) işlem tamamlanır.

#### Sorular:
1. **Algoritma Mantığı:**
   * Hızlı işaretçi (`fast`) neden $N$ adım önden başlatılır?
   * `fast` sona (`NULL`) ulaştığında yavaş işaretçinin (`slow`) tam olarak sondan $N$. düğümde olmasının matematiksel ispatını yazınız.
2. **Sınır Durumları:**
   * Kullanıcının girdiği $N$ değeri liste uzunluğundan büyükse ($N > L$) kodun segmentation fault almasını nasıl önlersiniz?
   * $N$ tam olarak liste uzunluğuna eşitse ($N = L$) fonksiyon listenin hangi elemanını döndürür?
3. **Uygulama:**
   * 7 elemanlı bir liste (`10, 20, 30, 40, 50, 60, 70`) üzerinde $N = 3$ (sondan 3. eleman olan `50`) ve $N = 10$ (sınır aşımı kontrolü) durumlarını test eden, `const Node* findNthFromEnd(const Node *head, int n)` prototipini içeren tam C programını yazınız ve terminal çıktısını ekleyiniz.

---

### 📌 Görev 3: Bağlı Listede Palindrom (Simetri) Doğrulama & $O(N^2)$ vs $O(N)$ Analizi
*Kaynak Örnek: `Linked_List_Example_17_Check_If_Polindrome.c`*

#### Genel Bilgi:
Bir veri diziliminin baştan ve sondan okunuşunun aynı olması durumuna **palindrom** denir (örneğin: `1 -> 2 -> 3 -> 2 -> 1`). Dizilerde bu kontrol iki uçtan (`left = 0`, `right = size - 1`) ortaya doğru gelerek $O(N)$ sürede ve $O(1)$ ek bellekle kolayca yapılır. Ancak tek yönlü bağlı listede geriye doğru gidiş bulunmadığından naif algoritmalar her adımda baştan yürümek zorunda kalır.

#### Sorular:
1. **Naif Algoritmanın Darboğazı ($O(N^2)$):**
   * İncelenen `Linked_List_Example_17_Check_If_Polindrome.c` kodundaki `palin_check` fonksiyonunda her döngü adımında `front = rear = p;` yapılarak baştan başlanmaktadır. 
   * $N$ elemanlı bir listede ilk ve son elemanları karşılaştırmak için $0 + (N-1)$, ikinci elemanlar için $1 + (N-2)$ adım atılmaktadır. Toplam adım sayısını $\sum$ formülüyle hesaplayarak algoritmanın neden $O(N^2)$ olduğunu gösteriniz.
2. **Optimizasyon Stratejisi ($O(N)$):**
   * Bu işlemi $O(N)$ doğrusal zamana indirmek için Call-Stack (Özyineleme / Recursion) mantığını açıklayınız. Fonksiyon çağrı yığını (call stack) geri dönerken listenin sonundan başına doğru nasıl bir simetri kontrolü sağlar?
3. **Uygulama:**
   * Hem bir palindrom listeyi (`1 -> 2 -> 3 -> 2 -> 1`) hem de palindrom olmayan bir listeyi (`1 -> 2 -> 3 -> 4 -> 5`) test eden, naif yöntem ile özyinelemeli $O(N)$ yöntemi karşılaştıran tam C programını yazınız ve terminal çıktısını ekleyiniz.

---

## 📄 DYS Teslim Şablonu

```text
================================================================================
                        MUĞLA SITKI KOÇMAN ÜNİVERSİTESİ
                             MÜHENDİSLİK FAKÜLTESİ
                          YAZILIM MÜHENDİSLİĞİ BÖLÜMÜ
                   SE2009 - LAB 05 EK ÖDEV DEĞERLENDİRME RAPORU
================================================================================

ÖĞRENCİ BİLGİLERİ:
• Adı Soyadı: [Adınız Soyadınız]
• Öğrenci No: [Öğrenci Numaranız]
• GitHub Kullanıcı Adı: se-[Öğrenci Numaranız]
• Teslim Tarihi: [GG/AA/YYYY]
• OneDrive Bağlantısı: [Erişilebilir OneDrive Linkiniz]

--------------------------------------------------------------------------------
GÖREV 1: İKİ LİSTENİN YERİNDE BİRLEŞTİRİLMESİ (CONCATENATION)
Cevabınız & Kod:
(Dizi kopyalaması ile bağlı liste gösterici bağlama farkını açıklayınız.
Liste 1'in boş olması durumunu analiz ediniz. Tam C kodunu ve çıktıyı yapıştırınız.)


GÖREV 2: RUNNER TEKNİĞİ İLE SONDAN N. ELEMANI BULMA
Cevabınız & Kod:
(Hızlı ve yavaş işaretçi mekanizmasının matematiksel ispatını yazınız.
N > L sınır kontrolünü açıklayınız. Tam C kodunu ve çıktıyı yapıştırınız.)


GÖREV 3: PALİNDROM DOĞRULAMA & O(N^2) - O(N) ANALİZİ
Cevabınız & Kod:
(Naif algoritmanın iç içe döngü adımlarını ve O(N^2) maliyetini gösteriniz.
Özyinelemeli O(N) çözümün Call-Stack mantığını açıklayınız. C kodunu ve çıktıyı yapıştırınız.)

================================================================================
```

