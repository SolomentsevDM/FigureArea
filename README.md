# FigureArea
Курсовая по дисциплине "Конструирование программного обеспечения". Программа для построения простых геометрических фигур и вычисления их площади.
Формула площади Гаусса (формула землемера или формула шнурования, или алгоритм шнурования) — формула определения площади простого многоугольника, вершины которого заданы декартовыми координатами на плоскости. В формуле векторным произведением координат и сложением определяется площадь области, охватывающей многоугольник, а затем из нее вычитается площадь окружающего многоугольника, что дает площадь многоугольника внутри.
Формула может быть представлена следующим выражением:
 
где
 

Алгоритм поиска замкнутых контуров

В работе был использован алгоритм, предназначенный для поиска всех возможных замкнутых контуров, т.е. всех возможных фигур. Сам алгоритм является немного измененным алгоритмом поиска в глубину, с тем отличием, что при спуске вглубь графа формируется список прямых, из которых в конечном итоге будет сформирована новая фигура.

Основные программные решения

При добавлении на рабочую область новой прямой, она заносится в List всех прямых.
Класс прямой:
class Line
        {
            public Point LineStartPoint;
            public Point LineEndPoint;
            public Dictionary<int, Line> ConnectedLines = new Dictionary<int, Line>();
            public Line(Point Start,Point End)
            {
                LineStartPoint = Start;
                LineEndPoint = End;
            }
            public Line() { }
        },
где 
LineStartPoint – начальная точка прямой
LineEndPoint – конечная точка прямой
ConnectedLines – словарь, содержащий всех соседей текущей прямой
В случае, если новая прямая совпадает с уже существующей прямой, то она не добавится в лист всех прямых.
Когда количество прямых превысит 2, вызовется метод LineCycle(), в котором создается новый словарь, содержащий все прямые, краевые точки которых имеют хотя бы одно соединение с другими прямыми.
Dictionary<int, Line> LinesShape2 = new Dictionary<int, Line>();
            foreach(var item in Lines)
            { 
                LinesShape2.Add(Lines.IndexOf(item), item);
            }
            bool IsChanged=true;
            while (IsChanged)
            {
                IsChanged = false;
                foreach (var item in LinesShape2)
                {
                    if (IsLineAlone(item.Value, LinesShape2))
                    {
                        LinesShape2.Remove(item.Key);
                        IsChanged = true;
                        break;
                    }
                }
            },
где 
LinesShape2 – словарь, содержащий все прямые, краевые точки которых имеют хотя бы одно соединение с другими прямыми.
В данном алгоритме последовательно удаляются прямые, краевые точки которых не имеют хотя бы одно соединение с другими прямыми.
Когда количество прямых в новом словаре превысит 2, вызовется рекурсивный метод ListLine, который по окончании своей работы сформирует лист, который содержит все возможные замкнутые контуры, которые формируют фигуру.
private void ListLine(int CurrentLineIndex, Dictionary<int, Line> LinesShape)
        {  
            foreach(var item in LinesShape[CurrentLineIndex].ConnectedLines)
            {
                if (LinesShape.ContainsKey(item.Key))
                {
                    if (!CurrentShape.Contains(item.Key))
                    {
                        CurrentShape.Add(CurrentLineIndex);
                        ListLine(item.Key, LinesShape);
                    }
                    if(item.Key== StartLineIndex&& CurrentShape.Count>=2)
                    {
                        CurrentShape.Add(CurrentLineIndex);
                        Dictionary<int, Line> TmpDict = new Dictionary<int, Line>();
                        Dictionary<int, Line> ShapeDict = new Dictionary<int, Line>();
                        foreach (var dig in CurrentShape)
                        {
                            TmpDict.Add(dig, Lines[dig]);
                            ShapeDict.Add(dig, Lines[dig]);
                        }
                        bool IsCorrect = true;
                        bool IsShape = true;
                        bool IsChanged = true;
                        ShapeDict.Remove(ShapeDict.First().Key);
                        while (IsChanged)
                        {
                            IsChanged = false;
                            foreach (var line in ShapeDict)
                            {
                                if (IsLineAlone(line.Value, ShapeDict))
                                {
                                    ShapeDict.Remove(line.Key);
                                    IsChanged = true;
                                    break;
                                }
                            }
                        }
                        if(ShapeDict.Count>0)
                        {
                            IsShape = false;
                        }
                        foreach (var line in TmpDict)
                        {
                            if (IsLineAlone(line.Value, TmpDict))
                            {
                                IsCorrect = false;
                                break;
                            }
                        }
                        if (IsCorrect&&IsShape)
                        {
                            List<int> tmp = new List<int>();
                            foreach (var dig in CurrentShape)
                            {
                                tmp.Add(dig);
                            }
                            tmp.Sort();
                            if (!AllShapesContains(tmp))
                            {
                                AllShapes.Add(new List<int>(tmp));
                            }
                        }
                        CurrentShape.RemoveAt(CurrentShape.Count - 1);
                    }
                }
            }
            if (CurrentShape.Count > 0)
            {
                CurrentShape.RemoveAt(CurrentShape.Count - 1);
            }
            return;
        }
Класс фигуры:
class Shape
        {
            public List<Line>LinesList=new List<Line>();
            public double Square;
            public Shape() { }
            public Shape(List<Line> list)
            {
                foreach(var item in list)
                {
                    LinesList.Add(item);
                }
            }
            public void SquareCalc()
            {
                Square = 0;
                for(int i=0;i< LinesList.Count-1;i++)
                {
                    Square += LinesList[i].LineStartPoint.X * LinesList[i].LineEndPoint.Y;
                    Square -= LinesList[i].LineEndPoint.X * LinesList[i].LineStartPoint.Y;
                }
                Square += LinesList[LinesList.Count - 1].LineStartPoint.X * LinesList[0].LineStartPoint.Y- 
                    LinesList[LinesList.Count - 1].LineStartPoint.Y * LinesList[0].LineStartPoint.X;
                Square = Math.Abs(Square);
                Square /= 2.0;
            }
        },
где 
LinesList – лист, содержащий прямые, которые формируют замкнутый контур.
Square – площадь фигуры
