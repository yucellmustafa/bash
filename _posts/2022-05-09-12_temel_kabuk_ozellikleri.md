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

### 1.2.5. Yerele Özel Çeviri

`$` işareti ile öncelenmiş çift tırnaklı bir dizge (`$"dizge"`) yerel dile çevrilir. Eğer yerel ayarları `C` ya da `POSIX` ise `$` işareti yoksayılır. Yerel dile çevrilen dizgeden çift tırnaklar kaldırılmaz. Bazı sistemler `LC_MESSAGES` ortam değişkeni ile belirtilen yerelin ileti kataloglarını kullanırken bazıları da `TEXTDOMAIN` ortam değişkenindeki, genellikle .mo sonekli dosya ismi ile belirtilen ileti kataloğunu kullanır. `TEXTDOMAIN` değişkeni kullanıldığında, dosyanın bulunduğu yerin `TEXTDOMAINDIR` ortam değişkeni ile belirtilmesi gerekir. Bazıları da bu her iki değişkeni birlikte kullanabilir: `TEXTDOMAINDIR/LC_MESSAGES/LC_MESSAGES/TEXTDOMAIN`.`mo`.

## 1.3. Betiklerde Açıklamalar
shopt (sayfa: 52) yerleşik komutunun etkin olduğu etkileşimli veya etkileşimsiz tüm kabuklarda `#` karakteri ile başlayan bir sözcük ve satır sonuna kadar devamındaki tüm karakterler yoksayılır. `interactive_comments` seçeneği etkinleştirilmemiş bir etkileşimli kabukta açıklamalara izin verilmez. Bu seçenek etkileşimli kabuklarda öntanımlı olarak etkindir. Bir kabuğu etkileşimli hale getirmek için ne yapılması gerektiği Etkileşimli Kabuklar (sayfa: 70) bölümünde anlatılmıştır.

# 2. Kabuk Komutları

`echo a b c` gibi basit bir kabuk komutu, komutun kendisi ile kendisinden sonra gelen ve boşluklarla ayrılmış argümanlardan oluşur. Basit komutların çeşitli yollarla birarada kullanılmasıyla daha karmaşık kabuk komutları elde edilebilir: bir boruhattı ile birinci basit komutun çıktısı ikinci basit komuta girdi olarak aktarılabilir, bir döngü, bir koşullu ifade ya da başka gruplamalar altında birarada kullanılabilir.

## 2.1. Basit Komutlar

Bir basit komut en çok rastlanan komut çeşitidir. Boşluklarla ayrılmış sözcüklerden oluşur ve kabuk denetim işleçleri (sayfa: 9)nden biri ile onlanır. İlk sözcük genelde çalıştırılması istenen komuttur, kalanlar ise bu komutun argümanlarıdır. Bir basit komutun dönüş durumu (sayfa: 9), POSIX 1003.1 `waitpid` işlevi ile sağlanan bir çıkış durumu (sayfa:35) ya da komut bir n sinyali ile sonlandırılmışsa `128+n`’dir.

## 2.2. Boruhatları

Bir boruhattı, | karakteri ile ayrılmış basit komutlar dizisidir.
Bir boruhattının sözdizimi:

```bash
[time [–p]] [!] komut1 [| komut2 ...]
```

Bir boruhattındaki her komut bir kanal ile sonrakine bağlıdır. Böylece her komut bir önceki komutun çıktısını
okur.

`time` anahtar sözcüğü, boruhattındaki iş sonuçlandıkça istatistiklerin basılmasını sağlar. İstatistikler geçen zaman, kullanıcı ve komutun çalıştırılması sırasında kullanılan sistem zamanından oluşur. `–p` seçeneği POSIX çıktı biçemini değiştirmekte kullanılır. `TIMEFORMAT` (sayfa: 66) değişkeninde belirtilen biçemleme dizgesi ile zamanlama bilgilerinin gösterim biçimi ayarlanabilir. Bu biçemler hakkında bilgi Bash Değişkenleri (sayfa: 60) bölümünde bulunabilir. `time` anahtar sözcüğü ile kabuk yerleşikleri, kabuk işlevleri ve boruhatlarının zamanlama bilgileri alınabilir. Harici bir `time` komutu ile bu işlem bu kadar kolay olamıyor.

Bir boruhattı eşzamanlamasız (Komut Listeleri (sayfa: 15) bölümüne bakınız) çalıştırılmadığında kabuk, boruhattındaki tüm komutların işlerini bitirmesini bekleyecektir.

Bir boruhattındaki her komut kendi altkabuğunda (Komut Çalıştırma Ortamı (sayfa: 33) bölümüne bakınız) çalıştırılır. Bir boruhattının çıkış durumu `pipefail` seçeneği etkin olmadıkça son komutun çıkış durumudur (set Yerleşiği (sayfa: 49) bölümüne bakınız). `pipefail` etkinse, borutattının dönüş durumu sıfırdan farklı bir değerle çıkan son (en sağdaki) komutun değeridir, değilse ve tüm komutlar başarı ile sonlanmışsa dönüş durumu sıfırdır. Bir boruhattı anahtar sözcüklerden biri olan ! karakteri ile başlıyorsa, çıkış durumu, açıklanan çıkış durumunun mantıksal tersidir. Kabuk bir değer döndürmeden önce boruhattındaki tüm komutlar sonlanıncaya kadar bekler.

## 2.3. Komut Listeleri

Bir liste; `;`, `&`, `&&`, veya `||` karakteri ile ayrılmış ve isteğe bağlı olarak `;`, `&`, veya `newline` karakterlerinden biri ile sonlandırılmış bir ya da daha fazla sayıdaki boruhattından oluşur.

Bu listelerin işleçleri, `&&` ile `||` aynı öncelikte ve kendi aralarında aynı öncelikte olan `;` ile `&` işleçlerinden daha önceliklidir. Komutları ayırmak için kullanılan satırsonu karakterleri bir liste içinde iki nokta üstüste karakterlerinin eşdeğerleri kabul edilir.

Bir komut `&` denetim işleci ile sonlandırılmışsa, kabuk komutu eşzamanlamaksızın bir altkabukta çalıştırır. Bu artalanda çalışma olarak bilinir. Kabuk, komutun işini bitirmesini beklemez ve 0 (doğru) çıkış durumu ile döner. İş denetimi (sayfa: 83) etkin değilse ve herhangi bir dolaylı yönlendirmenin bulunmaması halinde eşzamanlamasız komutun girdisi `/dev/null`’daki girdi olur.

`;` ile ayrılmış komutlar sırayla çalıştırılır ve kabuk her komutun sonlanmasını bekler. Dönüş durumu son komutun çıkış durumudur.

`&&` ve `||` denetim işleçleri sırasıyla VE ve VEYA listeleri oluşturur. VE listesinin sözdizimi:

```komut1 && komut2```

`komut2` sadece ve sadece `komut1` sıfır çıkış durumu ile dönmüşse çalıştırılır. VEYA listesinin sözdizimi

```komut1 || komut2```

`komut2` sadece ve sadece `komut1` sıfırdan farklı bir çıkış durumu ile dönmüşse çalıştırılır. VE ve VEYA listelerinin çıkış durumu daima son komutun çıkış durumudur.

## 2.4. Birleşik Komutlar

Birleşik komutlar kabuk programlama oluşumlarıdır. Her oluşum bir anahtar sözcükle veya denetim işleci başlar ve sonlandırıcı bir anahtar sözcük veya işleç ile sonlanır. Bir birleşik komut ile ilişkili bir yönlendirme (bkz, Yönlendirmeler (sayfa: 29)) aksi belirtilmedikçe birleşik komut içindeki tüm komutlara uygulanır.

Bash komutları gruplamak ve onları tek bir birim olarak çalıştırmak için döngüleri, koşullu komutları ve gruplama mekanizmalarını sağlar.

## 2.4.1. Döngüler

Bash aşağıdaki döngüleri destekler.

**Bilgi**
Komutların sözdizimi içinde herhangi bir yerde ; karakterine rastlanırsa bu karakter bir ya da daha fazla sayıda satırsonu karakteri ile değiştirilir.

### 2.4.1.1. `until`

`until` deyiminin sözdizimi:

```until sınama–komutları; do artbileşen–komutlar; done```

`artbileşen–komutlar`, `sınama komutları`nın çıkış durumları sıfırdan farklı olduğu sürece çalıştırılır. Deyimin çıkış durumu ise ya `artbileşen–komutlar` içindeki son çalıştırılan komutun çıkış durumudur ya da hiç komut
çalıştırılmamışsa sıfırdır.

### 2.4.1.2. `while`

`while` deyiminin söz dizimi:

```while sınama–komutları; do artbileşen–komutlar; done```

`artbileşen–komutlar`,sınama komutlarının çıkış durumları sıfır olduğu sürece çalıştırılır. Deyimin çıkış durumu ise ya `artbileşen–komutlar` içindeki son çalıştırılan komutun çıkış durumudur ya da hiç komut çalıştırılmamışsa sıfırdır.

### 2.4.1.3. `for`

`for` deyiminin sözdizimi:

```for isim [in sözcük ...]; do komutlar; done```

Sözcükler yorumlanır ve sonuçlanan listenin isim ile bağlantılı her üyesi için komutlar çalıştırılır. Deyimin `in` `sözcük` parçası yoksa, `in` `"$@"` belirtilmiş gibi kümeyi oluşturan her parametre için komutlar birer kere çalıştırılır (Özel Parametreler (sayfa: 21) bölümüne bakınız). Deyimin dönüş durumu çalıştırılan son komutun çıkış durumudur. Sözcüklerin yorumlanmasından bir üye elde edilememişse bir komut çalıştırılmaz ve sıfır çıkış durumu oluşur.

`for` deyimi için aşağıdaki sözdizimi de desteklenmektedir:

```for (( ifade1 ; ifade2 ; ifade3 )) ; do komutlar ; done```

Önce, `ifade1` aritmetik ifadesi aşağıda açıklanan kurallara bağlı olarak değerlendirilir (Kabuk Aritmetiği (sayfa:74) bölümüne bakınız). `ifade2` artimetik ifadesinin değeri sıfır oluncaya kadar tekrar tekrar değerlendirilir. `ifade2` aritmetik ifadesinin sıfırdan farklı olduğu durumlarda komutlar çalıştırılır ve `ifade3` aritmetik ifadesi değerlendirilir. Verilmeyen ifade için 1 varmış gibi işlem yapılır. Dönüş değeri listedeki son çalıştırılan komutun çıkış durumudur. Geçersiz bir ifadenin varlığı halinde ise çıkış durumu yanlış olacaktır.

`break` (sayfa: 38) ve `continue` (sayfa: 39) deyimleri döngü denetiminde kullanılabilir.

## 2.4.2. Koşullu Çalıştırma

### 2.4.2.1. `if`

`if` deyiminin sözdizimi:

```bash
if sınama–komutları; then
    artbileşen–komutlar;
[elif diğer–sınama–komutları; then
    diğer–altbileşenler;]
[else
    karşı–altbileşenler;]
fi
```

`sınama–komutları` listesi yorumlandığında dönüş durumu sıfırsa, artbileşen–komutlar çalıştırılır; sıfırdan farklı ise `elif`’lerin `diğer–sınama–komutları` listesi yorumlanır ve bunların dönüş durumuna göre ya `diğer–altbileşenler` ya da `karşı–altbileşenler` çalıştırılır. Deyimin tamamının dönüş durumu çalıştırılmış olan son komutun çıkış durumudur. Bir komut çalıştırılmamışsa ve hiçbir koşul doğru sonuç vermemişse sıfır döner.

### 2.4.2.2. `case`

`case` deyiminin sözdizimi:

```case sözcük in [ [(] kalıp [| kalıp]...) komutlar ;;]... esac```

`case` deyimi `sözcük` ile eşleşen ilk `kalıp`’a karşı düşen `komutlar`’ı çalıştırır. Kabuk seçeneği `nocasematch` etkinse eşleşme alfabetik karakterlerin büyüklüklerine bakılmaksızın uygulanır (bkz, shopt Yerleşiği (sayfa: 52)).

Çok sayıda kalıp kullanılmışsa kalıpları ayırmak için `|` işleci, kalıp listesini sonlandırmak için ise `)` işleci kullanılır. Her `case` deyimi bir `;;` işleci ile sonlandırılmalıdır. `sözcük` bir `kalıp` ile eşleştirmeye çalışılmadan önce, ayrımlama uygulanır ve yaklaşık (˜), parametre, komut ve aritmetik yorumlamalarına tabi tutulur.

Herbiri `;;` işleci ile sonlandırılmış çok sayıda `case` deyimi ardarda kullanılabilir. Ancak bunların içinden sadece ilk `sözcük–kalıp` eşleşmesine bağlı `komutlar` çalıştırılır.

Aşağıdaki örnek betikte `case` deyimi hayvanların bazı özellikleri için kullanılmıştır:

```bash
echo –n "Bir hayvan ismi yazınız: "
read HAYVAN
echo –n "$HAYVAN "
case $HAYVAN in
    at | kedi | maymun) echo –n "dört bacaklıdır" ;;
    kanarya | kanguru)  echo –n "iki bacaklıdır" ;;
                    *)  echo –n "için bir veri yok" ;;
esac
```

Hiçbir `kalıp` eşleşmezse dönüş durumu sıfırdır. Aksi takdirde son çalıştırılan komutun çıkış durumu döndürülür.