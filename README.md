# Fuzz-lab
Отчет ЛР по фаззингу
**1) Контрольное суммирование**
   Посчитаем md5 сумму для исполняемого файла xml
   `md5sum --tag xml`
   ![md5](https://github.com/user-attachments/assets/8de4e26f-20c2-4e62-a79f-c7651ba1eec0)
**2) Провести фаззинг-тестирование с обратной связью**
   С помощью git clone скачаем AFLplusplus и соберем его с помощью make. В результате получаем
   ![image](https://github.com/user-attachments/assets/2eeab0e5-a6ff-499f-9b05-18e56045e911)
   Теперь скачаем проект, выбранный для фаззинга (tinyxml2)
   Как точку входа будем использовать 
   ![xmlcpp](https://github.com/user-attachments/assets/844a5f98-782d-4ba8-9a9d-3080f0bf794a)
   Соберем проект с точкой входа через CMake с использованием afl-gcc и afl-g++: создадим директорию build перейдем в нее и выполним
   ```
   $ CC=afl-gcc CXX=afl-g++ cmake .. 
   $ CC=afl-gcc CXX=afl-g++ cmake  --build .
   Результат получим в afqkt XMLParser, переименуем его в xml
```
   Cоздадим директорию cor и в ней стартовый ex.xml с содержимым:
   ```
   <?xml version="1.0"?>
<catalog>
   <book id="bk101">
      <author>Gambardella, Matthew</author>
      <title>XML Developer's Guide</title>
      <genre>Computer</genre>
      <price>44.95</price>
      <publish_date>2000-10-01</publish_date>
      <description>An in-depth look at creating applications 
      with XML.</description>
   </book>
   <book id="bk102">
      <author>Ralls, Kim</author>
      <title>Midnight Rain</title>
      <genre>Fantasy</genre>
      <price>5.95</price>
      <publish_date>2000-12-16</publish_date>
      <description>A former architect battles corporate zombies, 
      an evil sorceress, and her own childhood to become queen 
      of the world.</description>
   </book>
   <book id="bk103">
      <author>Corets, Eva</author>
      <title>Maeve Ascendant</title>
      <genre>Fantasy</genre>
      <price>5.95</price>
      <publish_date>2000-11-17</publish_date>
      <description>After the collapse of a nanotechnology 
      society in England, the young survivors lay the 
      foundation for a new society.</description>
   </book>
   <book id="bk104">
      <author>Corets, Eva</author>
      <title>Oberon's Legacy</title>
      <genre>Fantasy</genre>
      <price>5.95</price>
      <publish_date>2001-03-10</publish_date>
      <description>In post-apocalypse England, the mysterious 
      agent known only as Oberon helps to create a new life 
      for the inhabitants of London. Sequel to Maeve 
      Ascendant.</description>
   </book>
   <book id="bk105">
      <author>Corets, Eva</author>
      <title>The Sundered Grail</title>
      <genre>Fantasy</genre>
      <price>5.95</price>
      <publish_date>2001-09-10</publish_date>
      <description>The two daughters of Maeve, half-sisters, 
      battle one another for control of England. Sequel to 
      Oberon's Legacy.</description>
   </book>
   <book id="bk106">
      <author>Randall, Cynthia</author>
      <title>Lover Birds</title>
      <genre>Romance</genre>
      <price>4.95</price>
      <publish_date>2000-09-02</publish_date>
      <description>When Carla meets Paul at an ornithology 
      conference, tempers fly as feathers get ruffled.</description>
   </book>
   <book id="bk107">
      <author>Thurman, Paula</author>
      <title>Splish Splash</title>
      <genre>Romance</genre>
      <price>4.95</price>
      <publish_date>2000-11-02</publish_date>
      <description>A deep sea diver finds true love twenty 
      thousand leagues beneath the sea.</description>
   </book>
   <book id="bk108">
      <author>Knorr, Stefan</author>
      <title>Creepy Crawlies</title>
      <genre>Horror</genre>
      <price>4.95</price>
      <publish_date>2000-12-06</publish_date>
      <description>An anthology of horror stories about roaches,
      centipedes, scorpions  and other insects.</description>
   </book>
   <book id="bk109">
      <author>Kress, Peter</author>
      <title>Paradox Lost</title>
      <genre>Science Fiction</genre>
      <price>6.95</price>
      <publish_date>2000-11-02</publish_date>
      <description>After an inadvertant trip through a Heisenberg
      Uncertainty Device, James Salway discovers the problems 
      of being quantum.</description>
   </book>
   <book id="bk110">
      <author>O'Brien, Tim</author>
      <title>Microsoft .NET: The Programming Bible</title>
      <genre>Computer</genre>
      <price>36.95</price>
      <publish_date>2000-12-09</publish_date>
      <description>Microsoft's .NET initiative is explored in 
      detail in this deep programmer's reference.</description>
   </book>
   <book id="bk111">
      <author>O'Brien, Tim</author>
      <title>MSXML3: A Comprehensive Guide</title>
      <genre>Computer</genre>
      <price>36.95</price>
      <publish_date>2000-12-01</publish_date>
      <description>The Microsoft MSXML3 parser is covered in 
      detail, with attention to XML DOM interfaces, XSLT processing, 
      SAX and more.</description>
   </book>
   <book id="bk112">
      <author>Galos, Mike</author>
      <title>Visual Studio 7: A Comprehensive Guide</title>
      <genre>Computer</genre>
      <price>49.95</price>
      <publish_date>2001-04-16</publish_date>
      <description>Microsoft Visual Studio 7 is explored in depth,
      looking at how Visual Basic, Visual C++, C#, and ASP+ are 
      integrated into a comprehensive development 
      environment.</description>
   </book>
</catalog>![рез](https://github.com/user-attachments/assets/c2c833e3-45f4-49ec-a686-f4bb48f4526c)

```
Запустим фаззинг 
`afl-fuzz -i сщк -o out -- ./xml @@`
Через ~1 час посмотрим результат
![Uploading рез.png…]()
Результаты записаны в `out/default`, где в директории `queue` хранятся мутированные корпуса 
![корпуса](https://github.com/user-attachments/assets/10f0b6c6-eb89-4340-b192-99543be433a6)
**3) Покрытие**
Пересоберем с использованием g++ coverage `g++ -o xml_parser xml.cpp -ltinyxml2 -coverage`
И пройдемся по корпусам для создания покрытия `for file in /home/p/out/default/queue/*; do ./xml_parser $file; done`
Теперь с помощью lcov создадим отчет о покрытии `lcov -c -d . -o cov.info`
И представим его в html файле `genhtml -o cov_data cov.info` 
![cjver](https://github.com/user-attachments/assets/0bf79721-9c6b-47a3-924c-2972081900c7)



