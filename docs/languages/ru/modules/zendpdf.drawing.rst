.. EN-Revision: none
.. _zendpdf.drawing:

Рисование
=========

.. _zendpdf.drawing.geometry:

Геометрия
---------

PDF использует ту же самую геометрию, что и PostScript. Она начинается
с нижнего левого угла страницы и по умолчанию измеряется в
пойнтах (1/72 дюйма).

Размер страницы может быть получен из объекта страницы:

.. code-block:: php
   :linenos:

   <?php
   $width  = $pdfPage->getWidth();
   $height = $pdfPage->getHeight();

.. _zendpdf.drawing.color:

Цвета
-----

PDF имеет мощные возможности для представления цветов. Модуль
ZendPdf поддерживает шкалу серого цвета, цветовые пространства RGB
и CMYK. Они могут использоваться в любом месте, где требуется
объект *ZendPdf\Color*. Классы *ZendPdf\Color\GrayScale*, *ZendPdf\Color\RGB* и
*ZendPdf\Color\CMYK* предоставляют этот функционал:

.. code-block:: php
   :linenos:

   <?php
   // $grayLevel (число с плавающей точкой)
   // 0.0 (черный) - 1.0 (белый)
   $color1 = new ZendPdf\Color\GrayScale($grayLevel);

   // $r, $g, $b (числа с плавающей точкой)
   // 0.0 (минимальная интенсивность) - 1.0 (максимальная интенсивность)
   $color2 = new ZendPdf\Color\RGB($r, $g, $b);

   // $c, $m, $y, $k (числа с плавающей точкой)
   // 0.0 (минимальная интенсивность) - 1.0 (максимальная интенсивность)
   $color3 = new ZendPdf\Color\CMYK($c, $m, $y, $k);

.. _zendpdf.drawing.shape-drawing:

Рисование фигур
---------------

Все операции прорисовки могут быть выполнены в контексте
страницы PDF.

Класс *ZendPdf\Page* предоставляет набор примитивов для рисования:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Рисует линию от x1,y1 до x2,y2.
    *
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    */
   public function drawLine($x1, $y1, $x2, $y2);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Рисует прямоугольник.
    *
    * Fill types:
    * ZendPdf\Const::SHAPEDRAW_FILLNSTROKE - заполнить прямоугольник и заштриховать (значение по умолчанию)
    * ZendPdf\Const::SHAPEDRAW_STROKE      - заштриховать прямоугольник
    * ZendPdf\Const::SHAPEDRAW_FILL        - заполнить прямоугольник
    *
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    * @param integer $fillType
    */
   public function drawRectangle($x1, $y1, $x2, $y2, $fillType = ZendPdf\Const::SHAPEDRAW_FILLNSTROKE);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Риcует многоугольник.
    *
    * Если $fillType (тип заполнения) равен ZendPdf\Const::SHAPEDRAW_FILLNSTROKE
    * или ZendPdf\Const::SHAPEDRAW_FILL, то многоугольник будет автоматически замкнут.
    * См. более подробное описание этих методов в документации PDF
    * (section 4.4.2 Path painting Operators, Filling)
    *
    * @param array $x  - массив чисел с плавающей точкой (X-координаты вершин)
    * @param array $y  - массив чисел с плавающей точкой (Y-координаты вершин)
    * @param integer $fillType
    * @param integer $fillMethod
    */
   public function drawPolygon($x, $y,
                               $fillType = ZendPdf\Const::SHAPEDRAW_FILLNSTROKE,
                               $fillMethod = ZendPdf\Const::FILLMETHOD_NONZEROWINDING);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Рисует окружность, центр которой находится в точке с координатами x и y,
    * с радиусом radius.
    *
    * Углы задаются в радианах.
    *
    * Сигнатуры метода:
    * drawCircle($x, $y, $radius);
    * drawCircle($x, $y, $radius, $fillType);
    * drawCircle($x, $y, $radius, $startAngle, $endAngle);
    * drawCircle($x, $y, $radius, $startAngle, $endAngle, $fillType);
    *
    *
    * Это не настоящая окружность, так как PDF поддерживает только кубические кривые Безье.
    * Но в очень хорошем приближении.
    * Она отличается от реальной окружности максимум на 0.00026 доли радиуса
    * (на углах PI/8, 3*PI/8, 5*PI/8, 7*PI/8, 9*PI/8, 11*PI/8, 13*PI/8 и 15*PI/8).
    * На углах 0, PI/4, PI/2, 3*PI/4, PI, 5*PI/4, 3*PI/2 и 7*PI/4 это точная касательная к окружности.
    *
    * @param float $x
    * @param float $y
    * @param float $radius
    * @param mixed $param4
    * @param mixed $param5
    * @param mixed $param6
    */
   public function  drawCircle($x, $y, $radius, $param4 = null, $param5 = null, $param6 = null);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Рисует эллипс внутри заданного прямоугольника.
    *
    * Сигнатуры метода:
    * drawEllipse($x1, $y1, $x2, $y2);
    * drawEllipse($x1, $y1, $x2, $y2, $fillType);
    * drawEllipse($x1, $y1, $x2, $y2, $startAngle, $endAngle);
    * drawEllipse($x1, $y1, $x2, $y2, $startAngle, $endAngle, $fillType);
    *
    * Углы задаются в радианах.
    *
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    * @param mixed $param5
    * @param mixed $param6
    * @param mixed $param7
    */
   public function drawEllipse($x1, $y1, $x2, $y2, $param5 = null, $param6 = null, $param7 = null);

.. _zendpdf.drawing.text-drawing:

Написание текста
----------------

Операции по написанию текста также существуют в контексте
страницы PDF.

.. code-block:: php
   :linenos:

   <?php
   /**
    * Выводит строку текста в заданной позиции.
    *
    * @param string $text
    * @param float $x
    * @param float $y
    * @throws ZendPdf\Exception
    */
   public function drawText($text, $x, $y );

Для написания текста используются текущий шрифт и его текущий
размер. См. подробное описание ниже.

.. _zendpdf.drawing.using-fonts:

Применение шрифтов
------------------

Метод *ZendPdf\Page::drawText()* использует текущий шрифт, который может
быть установлен методом *ZendPdf\Page::setFont()*:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает текущий шрифт.
    *
    * @param ZendPdf\Font $font
    * @param float $fontSize
    */
   public function setFont(ZendPdf\Font $font, $fontSize);

PDF поддерживает Type1, TrueType, Type3 и составные шрифты. Он
предоставляет еще 14 стандартных шрифтов Type1. На данный момент
модуль *ZendPdf* предусматривает только эти стандартные шрифты.
Они могут быть получены с помощью класса *ZendPdf\Font\Standard*.
Конкретный шрифт указывается в качестве аргумента
конструктора.

.. rubric:: Создание стандартного шрифта

.. code-block:: php
   :linenos:

   <?php
   ...
   // Создание нового шрифта
   $font = new ZendPdf\Font\Standard(ZendPdf\Const::FONT_HELVETICA);

   // Применение шрифта
   $pdfPage->setFont($font, 36);
   ...
   ?>

Константы для 14 стандартных шрифтов определены с помощью
класса *ZendPdf\Const*:

   - ZendPdf\Const::FONT_TIMES_ROMAN

   - ZendPdf\Const::FONT_TIMES_BOLD

   - ZendPdf\Const::FONT_TIMES_ITALIC

   - ZendPdf\Const::FONT_TIMES_BOLDITALIC

   - ZendPdf\Const::FONT_HELVETICA

   - ZendPdf\Const::FONT_HELVETICA_BOLD

   - ZendPdf\Const::FONT_HELVETICA_ITALIC

   - ZendPdf\Const::FONT_HELVETICA_BOLDITALIC

   - ZendPdf\Const::FONT_COURIER

   - ZendPdf\Const::FONT_COURIER_BOLD

   - ZendPdf\Const::FONT_COURIER_ITALIC

   - ZendPdf\Const::FONT_COURIER_BOLDITALIC

   - ZendPdf\Const::FONT_SYMBOL

   - ZendPdf\Const::FONT_ZAPFDINGBATS



.. _zendpdf.drawing.image-drawing:

Рисование изображений
---------------------

Класс предоставляет метод *drawImage()* для рисования изображений.

.. code-block:: php
   :linenos:

   <?php
   /**
    * Рисует изображение в заданной позиции на странице.
    *
    * @param ZendPdf\Resource\Image $image
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    */
   public function drawImage(ZendPdf\Resource\Image $image, $x1, $y1, $x2, $y2);

Объекты изображений должны создаваться через метод
*ZendPdf\Image::imageWithPath($filePath)* (сейчас поддерживаются изображения JPG,
PNG и TIFF):

.. rubric:: Рисование изображения

.. code-block:: php
   :linenos:

   <?php
   ...
   // Загрузка изображения
   $image = ZendPdf\Image::imageWithPath('my_image.jpg');

   $pdfPage->drawImage($image, 100, 100, 400, 300);
   ...
   ?>

**Важно! Для поддержки JPEG необходимо сконфигурировать
расширение GD.** **Важно! Для поддержки PNG необходимо
сконфигурировать расширение ZLIB для работы с изображениями с
Альфа-каналом.**

См. документацию PHP за более подробной информацией
(http://www.php.net/manual/en/ref.image.php). (http://www.php.net/manual/en/ref.zlib.php).

.. _zendpdf.drawing.line-drawing-style:

Стили рисования линий
---------------------

Стили рисования линий определяются толщиной линии, цветом
линии и шаблоном пунктира. Все эти параметры могут быть
определены методами класса *ZendPdf\Page*.

.. code-block:: php
   :linenos:

   <?php
   /** Установка цвета линии. */
   public function setLineColor(ZendPdf\Color $color);

   /** Установка толщины линии. */
   public function setLineWidth(float $width);

   /**
    * Установка шаблона пунктира.
    *
    * $pattern (шаблон) является массивом чисел с плавающей точкой:
    * array(on_length, off_length, on_length, off_length, ...)
    * $phase (фаза) является сдвигом от начала линии.
    *
    * @param array $pattern
    * @param array $phase
    */
   public function setLineDashingPattern($pattern, $phase = 0);

.. _zendpdf.drawing.fill-style:

Стиль заполнения
----------------

Методы *ZendPdf\Page::drawRectangle()*, *ZendPdf\Page::drawPoligon()*, *ZendPdf\Page::drawCircle()* и
*ZendPdf\Page::drawEllipse()* принимают аргумент ``$fillType`` как
необязательный параметр. Это может быть:

- ZendPdf\Const::SHAPEDRAW_STROKE - штрихует фигуру

- ZendPdf\Const::SHAPEDRAW_FILL - заполняет фигуру

- ZendPdf\Const::SHAPEDRAW_FILLNSTROKE - заполняет и штрихует (поведение по
  умолчанию)

Метод *ZendPdf\Page::drawPoligon()* принимает дополнительный параметр
``$fillMethod``:

- ZendPdf\Const::FILLMETHOD_NONZEROWINDING (поведение по умолчанию)

  :t:`Справка по PDF`  описывает это правило следующим образом:
  | Правило ненулевого количества витков определяет, находится
  ли данная точка внутри траектории путем мысленного
  проведения луча из этой точки в бесконечность в любом
  направлении и последующего определения мест, где участок
  траектории пересекает луч. Начиная отсчет с нуля, правило
  добавляет 1 каждый раз, когда участок траектории пересекает
  луч слева направо и отнимает 1 каждый раз, когда участок
  траектории пересекает участок справа налево. Если после
  подсчета всех пересечений результатом будет 0, то точка
  находится вне траектории, иначе — внутри траектории.
  Примечание: Метод не указывает, что делать, если участок
  траектории совпадает или является касательной к выбранному
  лучу. Поскольку направление луча является произвольным,
  правило просто выбирает луч, который не создает таких
  пересечений. Для простых выпуклых траекторий правило
  ненулевого количества витков определяет внутреннюю и
  внешнюю части так, как это интуитивно предполагается. Более
  интересными случаями являются те, которые включают в себя
  сложные или самопересекающиеся траектории, как, например, на
  Рис. 4.10 (в справке по PDF). Для траектории, представляющую собой
  пятиконечную звезду, состоящую из пяти соединенных отрезков,
  правило считает внутренней частью всю площадь, окруженную
  звездой, включая пятиугольник в центре. Для траектории,
  состоящей из двух концентрических окружностей, площадь,
  окруженная обеими окружностями, считается внутренней в том
  случае, если обе окружности нарисованы в одном и том же
  направлении. Если окружности нарисованы в противоположных
  направлениях, то, согласно правилу, только фигура в виде
  баранки между ними будет внутренней частью, "дырка" будет
  внешней частью.



- ZendPdf\Const::FILLMETHOD_EVENODD

  :t:`Справка по PDF`  описывает это правило следующим образом:
  | Альтернативой правилу ненулевого количества витков
  является
  правило чета-нечета. Это правило определяет нахождение точки
  проведением луча из этой точки в любом направлении и простым
  подсчетом количества пересечений частей траектории с этим
  лучом, независимо от направления пересечения. Если число
  нечетное, то точка находится во внутренней части; если четное,
  то точка находится снаружи. Это правило дает такой же
  результат, как и правило ненулевого количества витков для
  траекторий с простыми фигурами, но дает разные результаты в
  случае более сложных фигур. Рис. 4.11 (в справке по PDF) показывает
  результаты применения правила чета-нечета к сложным фигурам.
  В случае пятиконечной звезды правило считает точки
  треугольников находящимися внутри траектории, но не
  пятиугольник в центре. Для двух концентрических окружностей
  только фигура в виде баранки будет считаться внутренней
  частью, независимо от направлений, в которых нарисованы
  окружности.



.. _zendpdf.drawing.rotations:

Поворот
-------

Страница PDF может быть повернута перед применением любых
операций рисования. Это может быть сделано методом
*ZendPdf\Page::rotate()*:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Поворачивает страницу вокруг точки ($x, $y) на заданный угол (в радианах).
    *
    * @param float $angle
    */
   public function rotate($x, $y, $angle);

.. _zendpdf.drawing.save-restore:

Сохранение/восстановление графического состояния
------------------------------------------------

В любое время графическое состояние страницы (текущий шрифт,
размер шрифта, цвет линии, цвет заполнения, стиль линии,
поворот страницы, область ограничения) может быть сохранено и
после восстановлено. Операция сохранения сохраняет данные в
стек графического состояния, операция восстановления
извлекает данные из стека.

Методы в классе *ZendPdf\Page* для этих операций:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Сохраняет графическое состояние данной страницы.
    * Выполняет снимок используемых на данный момент стилей, положений, ... и всех
    * поворотов/преобразований/масштабирований которые были применены.
    * This takes a snapshot of the currently applied style, position, clipping area and
    * any rotation/translation/scaling that has been applied.
    */
   public function saveGS();

   /**
    * Восстанавливает графическое состояние, которое было сохранено последним
    * вызовом saveGS().
    */
   public function restoreGS();

.. _zendpdf.drawing.clipping:

Ограничение области рисования
-----------------------------

PDF и модуль ZendPdf поддерживают ограничение области рисования.
Ограничение определяет область страницы, затрагиваемой
операциями рисования. Вначале эта область представляет собой
всю страницу.

Класс *ZendPdf\Page* предоставляет набор методов для операций
ограничения.

.. code-block:: php
   :linenos:

   <?php
   /**
    * Делит текущую площадь ограничения с помощью прямоугольника.
    *
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    */
   public function clipRectangle($x1, $y1, $x2, $y2);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Делит текущую площадь ограничения с помощью многоугольника.
    *
    * @param array $x  - массив чисел с плавающей точкой (X-координаты верхушек)
    * @param array $y  - массив чисел с плавающей точкой (Y-координаты верхушек)
    * @param integer $fillMethod
    */
   public function clipPolygon($x, $y, $fillMethod = ZendPdf\Const::FILLMETHOD_NONZEROWINDING);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Делит текущую площадь ограничения с помощью окружности.
    *
    * @param float $x
    * @param float $y
    * @param float $radius
    * @param float $startAngle
    * @param float $endAngle
    */
   public function clipCircle($x, $y, $radius, $startAngle = null, $endAngle = null);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Делит текущую площадь ограничения с помощью эллипса.
    *
    * Сигнатуры метода:
    * drawEllipse($x1, $y1, $x2, $y2);
    * drawEllipse($x1, $y1, $x2, $y2, $startAngle, $endAngle);
    *
    * @todo обрабатывать случаи, когда $x2-$x1 == 0 или $y2-$y1 == 0
    *
    * @param float $x1
    * @param float $y1
    * @param float $x2
    * @param float $y2
    * @param float $startAngle
    * @param float $endAngle
    */
   public function clipEllipse($x1, $y1, $x2, $y2, $startAngle = null, $endAngle = null);

.. _zendpdf.drawing.styles:

Стили
-----

Класс *ZendPdf\Style* предоставляет набор функциональных
возможностей для работы со стилями.

Стили могут использоваться для сохранения набора параметров
графического состояния и применять их к странице PDF одной
операцией:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Установить стиль для будущих операций рисования на данной странице
    *
    * @param ZendPdf\Style $style
    */
   public function setStyle(ZendPdf\Style $style);

   /**
    * Возвращает стили, используемые на странице
    *
    * @return ZendPdf\Style|null
    */
   public function getStyle();

Класс *ZendPdf\Style* предоставляет набор методов для установки или
получения различных параметров графического состояния:

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает цвет линии.
    *
    * @param ZendPdf\Color $color
    */
   public function setLineColor(ZendPdf\Color $color);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает цвет линии.
    *
    * @return ZendPdf\Color|null
    */
   public function getLineColor();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает толщину линии.
    *
    * @param float $width
    */
   public function setLineWidth($width);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает толщину линии.
    *
    * @return float
    */
   public function getLineWidth();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает шаблон пунктира.
    *
    * @param array $pattern
    * @param float $phase
    */
   public function setLineDashingPattern($pattern, $phase = 0);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает шаблон пунктира.
    *
    * @return array
    */
   public function getLineDashingPattern();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает фазу пунктира.
    *
    * @return float
    */
   public function getLineDashingPhase();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает цвет заполнения.
    *
    * @param ZendPdf\Color $color
    */
   public function setFillColor(ZendPdf\Color $color);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает цвет заполнения.
    *
    * @return ZendPdf\Color|null
    */
   public function getFillColor();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Устанавливает текущий шрифт.
    *
    * @param ZendPdf\Font $font
    * @param float $fontSize
    */
   public function setFont(ZendPdf\Font $font, $fontSize);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Изменяет текущий размер шрифта
    *
    * @param float $fontSize
    */
   public function setFontSize($fontSize);

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает текущий шрифт.
    *
    * @return ZendPdf\Font $font
    */
   public function getFont();

.. code-block:: php
   :linenos:

   <?php
   /**
    * Возвращает текущий размер шрифта.
    *
    * @return float $fontSize
    */
   public function getFontSize();



