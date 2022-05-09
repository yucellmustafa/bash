---
title: Temel Kabuk Özellikleri
author: Mustafa Yucel
date: 2022-05-09
category: kabuk 
layout: post
---

Bash, `Bourne–Again SHell` sözcüklerinden oluşturulmuş bir kısaltmadır. Bourne kabuğu, ilk olarak Stephen Bourne tarafından yazılmış olan geleneksel Unix kabuğudur. Bourne kabuğunun içerdiği tüm yerleşik komutlar Bash’de de bulunmaktadır. Değerleme ve ayrımlama kuralları ‘standart’ Unix kabuğu için posix belirtiminden alınmıştır.

Bu kısımda, kabuk bloklarının kurgulanmasına kısaca değinilmiştir: komutlar, denetim yapıları, kabuk işlevleri, kabuk parametreleri, kabuk açılımları, isimli dosyalarla doğrudan çalışılabilmeyi sağlayan yönlendirmeler ve kabuk komutlarının çalıştırılması.

# 1. Kabukta Sözdizimi
Kabuk bir girdiyi okuduktan sonra bir dizi işlem yürütür. Eğer girdi bir açıklama başlangıcı içeriyorsa, kabuk açıklama sembolünü (#) ve satırın kalanını yoksayar. Aksi takdirde kabuk, girdiyi okur ve onu sözcüklere ve işleçlere ayırır, ayrımlama kurallarını kullanarak sözcükleri ve karakterleri anlamlandırır.

Bundan sonra bu dizgecikleri komutlar ve bağlı yapılar olarak çözümler, özel anlamları olan sözcükleri ve karakterleri kaldırır, kalanları yorumlar, gerekiyorsa girdi ya da çıktıyı yönlendirir, belirtilen komutları çalıştırır, komutların çıkış durumu oluşturmasını bekler ve bu çıkış durumunu denetim veya işlem yapılabilmesi için çıktılar.

## 1.1. Kabuk İşlemleri
Aşağıdaki kısa açıklamada bir komutun okunması ve çalıştırılması sırasındaki kabuk işlemleri anlatılmıştır. Temel olarak, kabukşunları yapar:
    
1. Girdiyi bir dosyadan (sayfa: 35) veya `–c` çağrı seçeneğinin (sayfa: 67) bir argümanı olarak sağlanan bir dizgeden ya da kullanıcının uçbiriminden okur.
    
2. Ayrımlama (sayfa: 13) bölümünde açıklandığı gibi ayrımlama kurallarına uygun olarak girdiyi sözcüklere ve işleçlere ayırır. Bu dizgecikler metakarakterlerle ayrılır. Takma ad yorumlaması (sayfa: 75) da bu adımda uygulanır.
    
3. Dizgecikleri basit ve birleşik komutlar (sayfa: 14) olarak çözümler.

4. Yorumlanan dizgecikleri dosya listelerine (sayfa: 27), komutlara ve argümanlara ayırarak çeşitli kabuk açılımlarını (sayfa: 22) uygular.

5. Varsa gerekli yönlendirmeleri (sayfa: 29) uyguladıktan sonra yönlendirme işleçleri ile terimlerini argüman listesinden kaldırır.

6. Komutu çalıştırır (sayfa: 32).

7. İsteğe bağlı olarak komutun işini bitirmesini ve çıkış durumu (sayfa: 35) üretmesini bekler.

## 1.2. Ayrımlama
Ayrımlama işlemi kabukta, sözcükler ve karakterlerden özel anlamlandırmanın kaldırılması anlamında kullanılmıştır. Ayrımlama özel karakterlerin özel davranışlarını ortadan kaldırarak, onların anahtar sözcükler olarak tanımlanmasını ya da parametreler olarak algılanmasını engellemek için kullanılır. Kabuk metakarakterlerinin (sayfa: 9) herbirinin kabuğa özel anlamı vardır ve bunlar sadece kendileri olarak ele alınacaksa tırnak içine alınmalıdır. Komut geçmişine ilişkin özellikler kullanılacaksa geçmiş yorumlama (sayfa: 90) karakteri olan `!` işaretinin geçmiş yorumlaması yapılacakşeklinde algılanmasını engellemek için bu karakter tırnak içine alınmalıdır. Geçmiş yorumlaması ile ilgili daha ayrıntılı bilgi edinmek için Bash’in Geçmişsel Yetenekleri (sayfa: 111) bölümüne bakınız. Üç tane ayrımlama mekanizması vardır: önceleme karakteri, tek tırnak ve çift tırnak.

### 1.2.1. Önceleme karakteri
Tırnak içine alınmamış bir tersbölü işareti `\` Bash önceleme karakteri adını alır. Kendisinden sonra gelen bir karakterin ya da sayısal bir karakter sabitinin değeri `satırsonu` karakteri hariç korunur. Eğer bir satırın sonunda tek başına ve tırnak içine alınmamış bir `\` karakterini izleyen bir `satırsonu` karakteri varsa yani, satırın sonunda bir `\satırsonu` çifti varsa, bu satır ve altındaki satır tek bir satır olarak yorumlanır (Bu durumda bu çift girdiden kaldırılır yani yoksayılır).

### 1.2.2. Tek Tırnak
Tek tırnak (’) içine alınmış karakterlerin değeri korunur. Ancak tek tırnak karakteri, önceleme karakteri ile
öncelenmiş olsa bile, tek tırnak karakterleri arasında bulunamaz.

### 1.2.3. Çift Tırnak
Çift tırnak (") içine alınmış karakterlerin `$`, ‘, `\` ve geçmiş yorumlaması etkinse `!` karakterleri hariç değerleri korunur. Çift tırnak içine alınmış `$` ve `‘` karakterleri özel anlamlarını korurlar (Kabuk Yorumları (sayfa: 22) bölümüne bakınız). `\` karakteri ise `$, ‘, ", \ veya satırsonu` karakterlerini öncelemek için kullanılmışsa özel anlamını korur. Çift tırnak içindeki önceleme karakterlerinden bu karakterleri öncelemekte kullanılanları kaldırılır. Özel anlamı olmayan karakterleri öncelemekte kullanılan önceleme karakteri değişmeden kalır. Bir çift tırnak, önceleme karakteri ile öncelenerek çift tırnaklar arasında kullanılabilir. Geçmiş yorumlaması etkinse, çift tırnaklar içinde bir `!` varsa ve önceleme karakteri ile öncelenmiş değilse geçmiş yorumlaması uygulanır. Özel karakterler olan `* ve @` çift tırnaklar arasında özel anlama sahiptir (Kabuk Parametrelerinin Yorumlaması (sayfa: 24) bölümüne bakınız).

### 1.2.4. ANSI–C Ayrımlaması
`$’dizge’` biçimindeki sözcükler özel olarak ele alınır. Sözcük, içindeki ANSI C standardında belirtilen tersbölü öncelemeli karakterler yorumlanarak dizgeye dönüştürülür. Varsa tersbölü öncelemeli karakterler aşağıdaki gibi yorumlanır:

`\a` : uyarı (zil)

`\b` : gerisilme

`\e` : önceleme karakteri (ANSI C değil)

`\f` : sayfa ileri

`\n` : satırsonu

`\r` : satırbaşı

`\t` : yatay sekme

`\v` : düşey sekme

`\\` : tersbölü

`\’` : tek tırnak

`\nnn` : Sekizlik tabanda `nnn` (3 rakamlı bir sayı) olarak değeri verilen sekiz bitlik karakter

`\xHH` : Onaltılık tabanda `HH` (iki onaltılık rakam) olarak değeri verilen sekiz bitlik karakter

`\cx` : Bir `<Ctrl>–<x>` karakteri

Sonuç, dolar işareti yokmuşçasına tek tırnaklar içine alınır.