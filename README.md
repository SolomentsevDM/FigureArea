# FigureArea
Курсовая по дисциплине "Конструирование программного обеспечения". 
Программа для построения простых геометрических фигур и вычисления их площади.

Формула площади Гаусса (формула землемера или формула шнурования, или алгоритм шнурования) — формула определения площади простого многоугольника, вершины которого заданы декартовыми координатами на плоскости. В формуле векторным произведением координат и сложением определяется площадь области, охватывающей многоугольник, а затем из нее вычитается площадь окружающего многоугольника, что дает площадь многоугольника внутри.

Алгоритм поиска замкнутых контуров:

В работе был использован алгоритм, предназначенный для поиска всех возможных замкнутых контуров, т.е. всех возможных фигур. Сам алгоритм является немного измененным алгоритмом поиска в глубину, с тем отличием, что при спуске вглубь графа формируется список прямых, из которых в конечном итоге будет сформирована новая фигура.
