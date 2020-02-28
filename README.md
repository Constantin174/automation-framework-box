# Banjo (Lanit AT framework-box) 
[![license](https://img.shields.io/github/license/lanit-izh/automation-framework-box)]

## Общая информация
Образцовая реализация [Banjo Core](https://github.com/lanit-izh/automation-framework-core) с использованием TestNG, Cucumber и Yandex Allure. Подробная инструкция по установке и использованию содержится в [wiki проекта](https://github.com/lanit-izh/automation-framework-box/wiki)
Большая часть структуры обусловлена использованием фреймворка [Atlas](https://github.com/qameta/atlas), а так же нашим собственным опытом в имплементации подхода BDD и в частности фреймворка [Cucucmber](https://github.com/cucumber/cucumber)
### pages
В пакете `main/pages` раполагаются интерфейсы, являющиеся абстрактным представлением тестируемых web-страниц. В них описываются явно в виде методов с аннотацией @FindBy элементы страницы по принципу классического PageObject. Этот паттерн дополнен подходом из Atlas: типичные, часто используемые элементы с тривиальными локаторами, подключатся в виде наследования интерфейса, предоставляющего метод с уже прописанными относительными локаторами.
По соглашению название интерфейса страницы заканчивается на `Page`.
### html_elements 
В пакете `main/pages/html_elements` релизованы наиболее часто встречающиеся элементы интерфейса со стандартными локаторами. Каждый интерфейс элемента может содержать дефолтные методы по работе с этим элементом, характерные только для данного типа элемента.
Методы, предоставляющие доступ к элементу на странице по соглашению реализуются во вложенном интерфейсе `With[Element]`, например для кнопки Button вложенный интерфейс, предоставляющий методы для получения этого элемента, называется `WithButton`.
Реализованы следующие стандартные элементы интерфейса страниц:
* Button -- кнопка, кнопка с текстом
* CheckBox -- чекбокс 
* DatePicker -- поле с выбором даты
* DropDown -- выпадающий список
* Input -- поле ввода
* Link -- ссылка
* RadioButton -- кнопка-переключатель выбора из списка
* Text -- текстовое поле
* TextArea -- многострочное поле ввода
### domain
В зависимости от количества используемых в тестировании доменных имён, можно разделять их дополнительно. В некоторых случаях достаточно поделить страницы по пакетам. Характерные только для данной страницы элементы или блоки располагаются во вложенном пакете `blocks` и `elements`. Общие для нескольких страниц выносятся в вышележащие пакеты.
### blocks
Элементы на странице бывают конечными, такие как кнопка, поле ввода, но могут быть так же блочными, с вложенными элементами. В зависимости от этого они наследуются от  `UIElement` либо от `AbstractBlockElement`. При этом соглашение об использовании аналогично конечным элементам. Для предоставления стандартных методов получения блочного элемента, внутри объявляется вложенный интерфейс с префиксом With, который затем наследуется страницей или другим блочным элементом.
### steps
В пакете `test/java/steps` располагаются классы с определением шагов для Cucumber-сценариев. Для простоты ориентирования и уменьшения количества дублирующих шагов, они располагаются в подпакетах, соответствующих конечному элементу взаимодействия в шаге.
Базовые шаги реализованы в `CommonStepsLibrary`, их список и формулировки могут отличаться в каждой версии имплементации фреймворка.
### features
Сценарии располагаются в `test/resources/features`
### configs
Все основные настройки запуска расположены в пакете `test/resources/config`. 

## Использование
### Web-UI
1. Прописать основной адрес тестируемого приложения в параметре `site.url` в файле `application.properties`
2. Создать для каждой из тестируемых страниц отдельный интерфейс в пакете `main/java/pages`. Для удобства использования на родном языке, в аннотации `@Title` можно указать читаемое название страницы.
3. В страницах подключить подходящие стандартные элементы при помощи наследования *With*-интерфейсов, либо прописать свои методы согласно подходу [Atlas](https://github.com/qameta/atlas)
4. Создать feature-файл в пакете `test/resources/features` с произвольным названием. Первый шаг сценария обычно является открытием тестируемого приложения. Далее добавляются шаги действий и проверок. 
