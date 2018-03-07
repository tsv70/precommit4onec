# Утилита для автоматической обработки исходных файлов конфигурации, внешних отчетов и обработок для платформы 1С:Предприятие при помещении в репозиторий git

Данное решение базируется на идеях проекта [precommit1c](https://github.com/xDrivenDevelopment/precommit1c/releases), но является самостоятельным решением.

## Почему не precommit1c

Указанным продуктом пользовались долгое время, он очень хорош, но потребность в развитии и невозможность реализации некоторых сценариев работы в публичной версии сподвигли к реализации нового решения.

### Особенности данного решения:

- Возможность расширения функциональности под свои нужды без потери совместимости с публичной версией
- Централизованная установка и обновление скриптов без необходимости утяжелять репозиторий проекта
- Максимально использует возможности платформы 1С:Предприятие последних версий (тестировалось на 8.3.10+, возможно на версиях 8.3.8-9 тоже будет работать)
- Возможность хранить внешние отчеты, обработки и расширения с одинаковыми именами
- Корректно обрабатывается удаление файлов

## Установка

Установка на компьютер стандартна

- `opm install precommit4onec` или
- распаковать в нужный каталог архив репозитория

## Использование

Перед использованием необходима установка precommit-hook'а в репозиторий:

- для выполнения установки в конкретный репозиторий необходимо выполнить команду `precommit4onec install repo_name`
- для выполнения установки во все репозитории каталога необходимо выполнить команду`precommit4onec install folder_reps -r`

После этого, при каждом коммите будет выполняться обработка файлов.

В комплекте присутствуют следующие сценарии обработки файлов:

- `РазборОтчетОбработокРасширений` - при выполнении данного сценария, файлы внешних отчетов, обработок и расширений средствами платформы будут разложены на исходные файлы и помещены в каталог исходных файлов репозитория в подкаталоги, соответствующие расширениям. Например внешний отчет `ВнешнийОтчет1.erf` будет разложен в каталог `src\erf\ВнешнийОтчет1`
- `РазборОбычныхФормНаИсходники` - при выполнении данного сценария, файлы обычных форм (`Form.bin`) раскладываются на исходные файлы с помощью инструмента `v8unpack`
- `КорректировкаXMLФорм` - при выполнении данного сценария, файлы описаний форм (`Form.xml`) проверяются на наличие задублируровашихся ндексов элементов, которые образуются при объединениях. Если дубли есть, то они исправляются
- `ДобавлениеПробеловПередКлючевымиСловами` - при выполнении данного сценария, в файлах модулей перед ключевыми словами добавляются отсутсвующие пробелы. На данный момент обрабатывается только ключевое слово `Экспорт`.
- `УдалениеЛишнихКонцевыхПробелов` - при выполнении данного сценария, в файлах модулей удаляются лишние пробелы и табы в конце строки после `;`.
- `СортировкаДереваМетаданных` - при выполнении данного сценария, выполняется упорядочивание объектов метаданных в дереве за исключением подсистем, они остаются в том порядке, который задал разработчик.

## Изменение настроек

precommit4onec может читать настройки своей работы из специального конфигурационного файла.

Управление настройками происходит с использованием команды `configure`:

- Печать настроек - `precommit4onec configure -global`
- Сброс настроек на заводские - `precommit4onec configure -global -reset`
- Интерактивное изменение настроек - `precommit4onec configure -global -config`.

Предоставляется возможность в репозитории иметь свои, отличные от глобальных, настройки. Для этого необходимо вместо флага `-global` в указанных выше командах передавать параметр `-rep-path` с указанием пути к каталогу репозитория.

Конфигурирование дает возможности:

- Изменить список сценариев обработки файлов
- Активизировать алгоритм подключния сценариев из каталогов репозитория

## Расширение функциональности

Для создания нового сценария обработки файлов необходимо воспользваться шаблоном, находящимся в каталоге `СценарииОбработки` скрипта.

### Установка сценария для всех репозиториев

Чтобы сценарий работал для всех репозиториев необходимо

- сохранить файл сценария в каталог `СценарииОбработки`
- выполнить команду сброса настроек либо интерактивного изменения, где указать сценарий в списке загружаемых

### Установка сценария для конкретного репозитория

Чтобы сценарий работал в конкретном репозитории необходимо

- Решить, в каком каталоге в репозиториях будут хранится сценарии, например `tools\СценарииОбработки`
- Создать каталог в репозитории и скопировать в него файл сценария
- Вызвать команду конфигурирования, в которой включить использование сценариев из репозитория
- Указать имя каталога

Если при выполнении precommit4onec не найдет файлов сценариев в указанном каталоге, либо не найдет каталог, он об этом сообщит в лог и продолжит работу без ошибок.

## Ссылки

- [Шаблон скрипта](https://github.com/oscript-library/oscript-app-template)
- [precommit1c](https://github.com/xDrivenDevelopment/precommit1c/releases)
- [Библиотека os-скриптов](https://github.com/oscript-library)
