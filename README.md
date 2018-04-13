# spellchecker-russian
The implementation of levenshtein distance with n gram models. The code works with russian texts. 
В докладе представлен опыт реализации алгоритма автоматического исправления опечаток, основанного на расстоянии Левенштейна 
и n-граммной модели. В данной работе было выдвинуто предположение о том, что в большинстве случаев опечатки — это некорректный 
ввод слов с любого электронного устройства, имитирующего или имеющего компьютерную клавиатуру, поэтому в алгоритме учитывалось 
расстояние между клавишами стандартной клавиатуры русского языка. Результаты работы алгоритма позволили выявить достоинства 
и недостатки исходного предположения о некорректном вводе. 
Предположение о том, что опечатки возникают из-за некорректного ввода с клавиатуры электронных устройств, является 
методологической основой данной работы. Также вспомогательным материалом для создания кода послужила программа, находящаяся в 
открытом доступе на Github (https://github.com/Machyne/Spelling). С помощью средств языка Python была создана симметричная 
карта для всех пар букв с соответствующими им весами замен. Были учтены горизонтальная, вертикальная и диагональная близость 
букв на клавиатуре, а также схожие по звучанию буквы. Эти данные были адаптированы для русского языка. Например, для буквы “а” 
были подобраны следующие пары: по горизонтали - “а” - “п”; “а” - “в”; по вертикали - “а” - “м”; “а” - “с”; по диагонали - “а” -
“у”; “а” - “е”. Также были приняты во внимание ошибки, допускаемые независимо от расположения клавиш на клавиатуре, для “а” 
это такие пары как, “а” - “о”: “молоко” - “малако”; “а” - “я”: “чаща” - “чящя”; “а” - “и”: “маленький” - “маленькай”. К данной 
матрице был применен алгоритм расчета расстояния Левенштейна. Расстояние Левенштейна, или дистанция редактирования, - это 
минимальное количество операций (вставка, удаление или замена), которые направлены для преобразования одной строки в другую
кратчайшим способом. При применении данного алгоритма образуется матрица дистанций, в данном случае была получена матрица 
значений близости пар букв. В этой матрице дистанций приписаны числовые значения, которые зависят от того, в каком 
расположении от рассматриваемой буквы находятся все остальные, так, если буква “в” находится от “а” по горизонтали,
числовое значение будет равняться 1,5. Вес чисел подобран вручную и служит для обозначения только вероятности замены букв 
при вводе: чем ближе буква находится от нужной, значит, тем меньше вероятность написать правильно, поэтому меньшее число 
означает большую вероятность ошибки (таблица 1). В тех случаях, когда буквы в матрице совпадают, по умолчанию выбирается 
числовое значение 0, а для дополнительного значения, которое не зависит от близости клавиш, числовое значение определяется как 
2. Также для ввода лишней буквы или пропуска выбрано числовое значение 3, однако оно не представлено в матрице, поскольку 
реализуется непосредственно в формуле Левенштейна. Cледующим шагом было применение спеллчекера. Каждое предложение во входном 
тексте проходит токенизацию, осуществляемую при помощи инструментария библиотеки NLTK [6], в результате чего входной текст 
разбивается на отдельные значимые единицы (слова, символы, пробелы) для последующей компьютерной обработки. Затем создается 
второй список токенов, в который включаются только слова. Этот список инвертируется, и каждое слово в нем проверяется. 
В результате слову приписывается вероятность, которая представляет собой среднее вероятностей триграммы этого слова: 
самого слова, допустим B, предшествующего ему - A и следующего за ним - C, например, для слова “пошел” триграмма будет 
выглядеть следующим образом: яA пошелB гулятьC. Эти вероятности триграмм рассчитаны при помощи взвешенной линейной 
интерполяции (P) вероятностей униграмм, биграмм и триграмм, и представляют собой коэффициент счетов:
P(wi |wi-1...wn) = a1P1(wi) + a2P2(wi|wi-1) + … +an-1Pn-1(wi |wi -1...wn-1), где 0<=ai<=1 и ∑ i ai =1 , где Pi - статистическая
функция для i-грамм.
Каждому слову приписывается список лучших кандидатов на замену. Для того чтобы выявить наилучших кандидатов, сначала 
определяется весь набор кандидатов. Этот набор включает в себя каждое слово, которое может следовать за двумя предыдущими, и 
каждое слово, которое начинается с буквы, равной, «схожей», или горизонтально смежной по отношению к первой букве слова с 
предполагаемой опечаткой. Вес, приписанный любому кандидату, - это линейная комбинация расстояния для замены в ошибочном слове и 
вероятность, которую оно получит, если произойдет замена. Список лучших замен – это 10 первых кандидатов по счету. Наконец, 
если у слова крайне низкая вероятность, и программа не установила лучшего кандидата, то оно будет отмечено как «неправильное»,
и тогда пользователю будет предложено самому исправить отмеченное слово. 