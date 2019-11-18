[![Release](https://img.shields.io/github/tag/huxuxuya/FormModificator.svg?label=Last%20release&a)](https://github.com/huxuxuya/FormModificator/releases)

# Модуль изменения управляемых форм

При доработке типовых конфигураций, для уменьшения измержек на сопровождение конфигурации при ее обновлении, доработка форм должна производиться с максимальным приоритетом программной доработки.

Целью модуля является упрощенное добавление реквизитов формы, добавление их на форму (в том числе и таблиц), так же добавление групп, команд формы и кнопок к ним.

### Установка

Уставновка производится через сравнение/объединения конфигурации с файлом поставки, с установкой на поддержку.

#### Предварительные требования

Модуль распространяется поставкой. 
Зависимостей не имеет.

### Обновление

Обновление производится через обновление с сохранением конфигурации поставщика
Для проверки корректности совместимистои версий необходимо открыть обработку тестирования обновляемой версии(которая входит в поставку) на версии, на которую будет обновлен модуль.

## Начало работы

### Предварительная подготовка

Необходимо добавить вызов процедуры, содержащей код программного добавления в самое начало следующих процедур:
 * ПриСозданииНаСервере()
 * ПриЧтенииНаСервер() - если форма имеет возможность открытия существующего объекта
  
 <details>
  <summary>Пример кода подготовки</summary>
	
 ``` bsl
&НаСервере
Процедура ПриЧтенииНаСервере(ТекущийОбъект)
	ПрефиксПодготовитьФорму();
	//Текст процедуры	
КонецПроцедуры 	

 &НаСервере
Процедура ПриСозданииНаСервере(Отказ, СтандартнаяОбработка)
	ПрефиксПодготовитьФорму();
	//Текст процедуры	
КонецПроцедуры 

&НаСервере	
Процедура ПрефиксПодготовитьФорму()
	Если РедакторФорм.ФормаПодготовлена(ЭтаФорма) Тогда
		Возврат;
	КонецЕсли;
	//Код изменения формы
КонецПроцедуры 
```
</details>

**Данная архитектура необходима:**
* Для возможности отображения данных, которые находятся не в самом объекте. 
* Для корректной настройке формы, которая должна происходить при чтении объекта, до вызовая создания на сервере.

Так же данный подход позволяет использовать типовые процедуры, например **УправлениеФормой()**.

### Использование

#### Базовый пример использования
<details>
 <summary>Вариант заполнение свойств элемента после инициализации контекста.</summary>
	
``` bsl
КонтекстФормы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект);	
КонтекстФормы.Свойства.Вставить("Вид", ВидГруппыФормы.ОбычнаяГруппа);
КонтекстФормы.Свойства.Вставить("Группировка", ГруппировкаПодчиненныхЭлементовФормы.ГоризонтальнаяЕслиВозможно);
КонтекстФормы.Свойства.Вставить("ОтображатьЗаголовок", Ложь);
ЭлементГруппаШапка = РедакторФорм.ДобавитьГруппуНаФорму(КонтекстФормы, "ГруппаШапка"); 
```
</details>

<details>
 <summary>Вариант определение свойств до создания контекста.</summary>
	
``` bsl
Свойства = Новый Структура("Вид, ОтображатьЗаголовок", ВидГруппыФормы.ОбычнаяГруппа, Ложь);
КонтекстФормы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, , , Свойства);	
ЭлементГруппаШапка = РедакторФорм.ДобавитьГруппуНаФорму(КонтекстФормы, "ГруппаШапка"); 
```
</details>

### Программный интерфейс

* Добавление полей
	<details>
	 <summary>ДобавитьПолеНаФорму(КонтекстЭлемента, ИмяПоля)</summary>

	``` bsl
	КонтекстПоля = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, ГруппаЛево);	
	КонтекстПоля.Свойства.Вставить("ПутьКДанным", "Статус");
	РедакторФорм.ДобавитьПолеНаФорму(КонтекстПоля, "Статус");
	```
	</details>
	<details>
	 <summary>ДобавитьПолеНаФормуРеквизитФормы(КонтекстЭлемента, ИмяРеквизита)</summary>

	``` bsl
	КонтекстЭлемента = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, ГруппаПраво);
	РедакторФорм.ДобавитьПолеНаФормуРеквизитФормы(КонтекстЭлемента, "Состояние");
	```
	</details>	
* Добавление групп 
	<details>
	 <summary>ДобавитьГруппуНаФорму(КонтекстЭлемента, ИмяГруппы)</summary>

	``` bsl
	КонтекстСтраницы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект);
	КонтекстСтраницы.Свойства.Вставить("Вид", ВидГруппыФормы.Страницы);
	ГруппаСтраницы = РедакторФорм.ДобавитьГруппуНаФорму(КонтекстСтраницы, "ГруппаСтраницы");

	КонтекстСтраницы.Свойства.Вид = ВидГруппыФормы.Страница;	
	КонтекстСтраницы.Родитель = ГруппаСтраницы;
	КонтекстСтраницы.Свойства.Вставить("Заголовок", "Товары");
	СтраницаТовары = РедакторФорм.ДобавитьГруппуНаФорму(КонтекстСтраницы, "СтраницаТовары");	
	
	КонтекстСтраницы.Свойства.Вставить("Заголовок", "Услуги");
	СтраницаУслуги = РедакторФорм.ДобавитьГруппуНаФорму(КонтекстСтраницы, "СтраницаУслуги");
	```
	</details>	
	<details>
	 <summary>ДобавитьГруппуСКолонкамиЛевоИПраво(КонтекстЭлемента, ИмяОсновнойГруппы)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаОсновная);
	РедакторФорм.ДобавитьГруппуСКолонкамиЛевоИПраво(КонтекстГруппы, "Шапка");
	ШапкаЛево = Элементы.ШапкаЛево;
	ШапкаПраво = Элементы.ШапкаПраво;
	```
	</details>	
	<details>
	 <summary>ДобавитьГруппуОбычную(КонтекстЭлемента, ИмяЭлемента)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, ГруппаСтраницаТовары);
	ГруппаИтогов = РедакторФорм.ДобыавитьГруппуОбычную(КонтекстГруппы, "ГруппаИтогов");
	```
	</details>	 
* Добавление таблиц
	<details>
	 <summary>ДобавитьТаблицуНаФорму(КонтекстЭлемента, ИмяПоля, ПутьКДанным, СтруктураКолонок)</summary>

	``` bsl
	КонтекстГруппТаблицы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, ГруппаИтоговойТаблицы);
	
	СтруктураКолонок = Новый Структура;
	СтруктураКолонок.Вставить("ТаблицаКолонка1", "Колонка1");
	СтруктураКолонок.Вставить("ТаблицаКолонка2", "Колонка2");
	СтруктураКолонок.Вставить("ТаблицаКолонка3", "Колонка3");
	ЭлементТаблицаПроверряемыеМетоды = РедакторФорм.ДобавитьТаблицуНаФорму(КонтекстГруппТаблицы, "ИмяТаблицы", "Объект.ТаблицаОбъекта", СтруктураКолонок);	
	```
	</details>
	<details>
	 <summary>ДобавитьПолеТабличнойЧастиНаформу(КонтекстЭлемента, ПутьТабличнойЧасти, ИмяКолонкиТабличнойЧасти)</summary>

	``` bsl
	КонтекстФормы = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект);
	РедакторФорм.ДобавитьПолеТабличнойЧастиНаформу(КонтекстФормы, "Объект.ИмяТаблицы", "ИмяКолонки");
	```
	</details>	
* Добавление команд
	<details>
	 <summary>ДобавитьКнопкуНаФорму(КонтекстЭлемента, ИмяКнопки, ВидКнопки, ИмяКоманды)</summary>

	``` bsl
	КонтекстГруппыКнопок = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, ГруппаКнопок);
	РедакторФорм.ДобавитьКнопкуНаФорму(КонтекстГруппыКнопок, "ИмяКоманды", ВидКнопкиФормы.ОбычнаяКнопка, "Подключаемый_КомандаИмяКнопки");
	```
	</details>	 
	<details>
	 <summary>ДобавитьКомандуИГиперссылкуНаФорму(КонтекстЭлемента, ИмяКоманды, ПроцедураОбработки, ЗаголовокГиперссылки, ГруппаВывода)</summary>

	``` bsl
	КонтекстКоманды = РедакторФорм.СоздатьКонтекстЭлемента(ЭтотОбъект, Элементы.Группа);
	РедакторФорм.ДобавитьКомандуИГиперссылкуНаФорму(КонтекстКоманды, "ИмяКоманды", "Подключаемый_КомандаИмяКоманды", "Очистить все");
	```
	</details>	 

	 * ДобавитьКомандуИКнопкуКоманднойПанели(КонтекстЭлемента, ИмяКоманды, ЗаголовокКнопки) 
	 * ДобавитьКомандуИКнопкуНаФорму(КонтекстЭлемента, ИмяКоманды, ЗаголовокКнопки)
	 * ДобавитьКомандуНаФорму(КонтекстЭлемента, ИмяКоманды, ИмяДействия, Заголовок, Подсказка = "") 
* Добавление реквизитов объекта
	 * ДобавитьРеквизитОбъектаНаФорму(КонтекстЭлемента, ИмяРеквизита)
	 * ДобавитьПолеНаФормуРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита)
	 * ДобавитьПолеВШапкуФормыРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита)
	 * ДобавитьМногострочноеПолеРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита, Высота = 2)
	 * ДобавитьПолеФлажкаНаФормуРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита)
	 * ДобавитьГруппуКнопок(КонтекстЭлемента)

## Запуск тестов

Тесты запускаются через vanessa runner: файл run_vanessa.bat.

## Authors

See the list of [contributors](https://github.com/huxuxuya/FormModificator/contributors) who participated in this project.

