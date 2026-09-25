# Assignments Repository

Bu repo **haftalık ödev duyuruları** için merkezi depodur.

## Klasör Yapısı

```
├── week00/    # 0. Hafta (Hazırlık)
│   ├── prelab.md
│   └── lab.md
├── week01/    # 1. Hafta
│   ├── prelab.md
│   └── lab.md
├── week02/    # 2. Hafta
│   ├── prelab.md
│   └── lab.md
...
├── week10/    # 10. Hafta
│   ├── prelab.md
│   └── lab.md
└── README.md  # Bu dosya
```

## Kullanım

### Öğretim Elemanı (Siz)
1. İlgili haftanın klasörüne (`weekXX/`) ödev dosyalarını koyun
2. Commit + push yapın
3. Öğrencilere duyuru yapın

### Öğrenciler
1. Bu repoyu takip eder (Watch/Star)
2. Haftalık duyuru geldiğinde ilgili dosyaları kendi `student-lab-template` tabanlı repo'larına kopyalar
3. Kendi repo'larında çalışır, push yapar

## Örnek Çalışma Akışı

```bash
# Öğrenci kendi repo'sunu clone eder
git clone https://github.com/se2009-26/Labse-230717017.git
cd Labse-230717017

# Assignments reposunu remote olarak ekler
git remote add assignments https://github.com/se2009-26/assignments.git
git fetch assignments

# 1. hafta ödevlerini kendi repo'suna kopyalar
cp -r ../assignments/week01/* prelab01/ lab01/  # veya manuel kopyalar

# Çalışır, commitler, pushlar
git add prelab01/ lab01/
git commit -m "week01: completed prelab and lab"
git push origin main
```

## Notlar

- Bu repo **public** olabilir (herkes görebilir)
- Öğrenciler sadece okur, yazmaz
- Ödev dosyaları: markdown, pdf, kod şablonları, test dosyaları olabilir