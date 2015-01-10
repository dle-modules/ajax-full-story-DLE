# { ajax-full-story } DLE
Бесплатный модуль для загрузки полной новости в DLE средствами AJAX

## Требования
- Версия DLE: **10.2+** (на более старых не проверялся, но должен работать вплоть до 9.6)

## Особенности модуля
- Не требует каких-либо правок движка
- Учёт прав доступа к новости
- Подсчёт количества просмотров (если это разрешено)
- Корректная очистка кеша модуля
- Поддержка всех тегов
- Кеширование на стороне клиента (модуль отдаёт правильные заголовки)

## Установка
1. Если сайт работает в кодировке windows-1251, необходимо перекодировать файлы модуля в эту кодировку.
2. Залить содержимое папки **upload** в корень сайта.
3. В нужном месте любого шаблона вставить 
``` html
<span class="btn" data-fs-id="{news-id}">Быстрый просмотр</span>
```
где `{news-id}` -- ID новости (**обязательный параметр**)
4. В js файл шаблона вставить:
``` javascript
$(document).on('click', '[data-afs-id]', function () {
    var $this = $(this),
        $data = $this.data();

    $.ajax({
            url: dle_root + 'engine/ajax/full-story.php',
            type: 'GET',
            dataType: 'html',
            data: {
                newsId: $data.afsId, // Обязательная переменная
                preset: ($data.afsPreset) ? $data.afsPreset : '', // название файла с настройками
                template: ($data.afsTemplate) ? $data.afsTemplate : '', // Название файла с шаблоном
            },
        })
        .done(function (data) {
            var $html = $(data);

            // Данные получены, можно заняться разбором и показать их в диалоге
            // Ниже простейший пример вывода контента в стандартном модальном окне DLE

            var modalId = 'afs-' + $data.afsId + '-' + $data.afsPreset + '-' + $data.afsTemplate;
            modalId = modalId.replace(/\//g, "-");

            var $modalBlock = $('<div style="display: none;"><div id="' + modalId + '"></div></div>');

            $modalBlock
                .appendTo('body')
                .find('#' + modalId)
                .html($html)
                .dialog({
                    width: 800
                });

        })
        .fail(function () {
            console.log("full-story error");
        });
});
```

5. В CSS-файл шаблона вставить код для стилизации выводимых ошибок:
``` CSS
.afs-error {
    /*Общий стиль для всех ошибок*/
    padding: 20px;
    background: #fff;
    color: #424242;
}
.afs-news-error {
    /*Стиль ошибки, если новость не найдена*/
    background: #eceff1;
}
.afs-tpl-error {
    /*Стиль ошибки, если не найден шаблон*/
    color: #b71c1c;
}
.afs-perm-error {
    /*Стиль ошибки, если не достаточно прав для просмотра полной новости*/
    background: #e65100;
    color: #F5F5F5;
}
```
## Документация
- в работе.