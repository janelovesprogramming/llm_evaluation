# llm_evaluation

## 1. Скрипт для оценки ответов LLM

Модель: gemini-pro

Эталонный набор данных: MedQuAD

Размер набора данных: 14 560 записей

Для расчета метрик случайным образом было выбрано:  1007 записей

Gemini - семейство мультимодальных больших языковых моделей, разработанное Google DeepMind и являющееся преемником LaMDA и PaLM 2. Gemini Ultra, Gemini Pro и Gemini Nano были анонсированы 6 декабря 2023 года.

MedQuAD включает 14 560 пар медицинских вопросов-ответов c 12 сайтов NIH (например, cancer.gov, niddk.nih.gov, GARD, MedlinePlus Health Topics). Коллекция охватывает 37 типов вопросов (например, "Лечение", "Диагноз", "Побочные эффекты"), связанных с заболеваниями, лекарствами и другими медицинскими объектами, такими как тесты.

## 2. Метрики для оценки ответов LLM

Для оценки ответов LLM, были выбраны метрики: BLEU, ROUNGE-N, BERTScore, Семантическое сходство.

<p align="center">

<img width="977" alt="Screenshot 2024-02-16 at 17 09 36" src="https://github.com/janelovesprogramming/llm_evaluation/assets/35342454/2dbc013e-5781-467d-8a55-4794bd3847d8">
 Рисунок 1. Набор данных с метриками для оценки ответов LLM.
 </p>
 <p align="center">
<img width="1010" alt="Screenshot 2024-02-16 at 12 52 31" src="https://github.com/janelovesprogramming/llm_evaluation/assets/35342454/b7e939af-cf34-417b-9596-01931200246a">
  Рисунок 2. Таблица со средними значениями метрик.
 </p>


![newplot (2)](https://github.com/janelovesprogramming/llm_evaluation/assets/35342454/bf72937e-2b78-41d4-9880-c17f101c1f46)

 <p align="center">
Рисунок 3. Распределение метрики ROUGE-N.
 </p>

![newplot (3)](https://github.com/janelovesprogramming/llm_evaluation/assets/35342454/e80eebd4-8652-415e-9d8e-de522510d46e)

 <p align="center">
Рисунок 4. Распределение метрики семантическое сходство.
 </p>

## 3. Описание метрик 

### 3.1 BLEU

BLEU (Bilingual Evaluation Understudy) - это метрика, которая используется для оценки качества машинного перевода путем сравнения автоматически сгенерированных переводов с эталонными (человеческими) переводами. 

BLEU метрика рассчитывается на основе точности (precision) n-грамм в сгенерированном переводе:
```math
BLEU = BP * exp(\sum_{n=1}^N w_n log(p_n))
```

$BP$ - штраф за близость перевода, для того чтобы уменьшить влияние коротких переводов.

$p_n$- точность n-грамм.

$w_n$ - весовой коэффициент для каждой n-граммы.

$N$ - максимальная длина n-граммы (обычно используется до 4).

Интерпретация:
Чем выше значение BLEU, тем лучше сгенерированный перевод соответствует эталонному. Максимальное значение BLEU равно 1, что означает полное совпадение с эталонным переводом.

Плюсы:

+ Скорость вычисления.
+ Предоставляет количественную оценку качества перевода, что удобно для сравнения различных моделей.
  
Минусы:

- BLEU не всегда коррелирует с оценками, данных человеческими экспертами, так как она оценивает только сходство n-грамм и не учитывает семантическую точность или связность перевода.
- Может недооценивать качество перевода в случае, если сгенерированный перевод различается по стилю или выбору слов, но все же передает ту же самую информацию.
- BLEU не учитывает контекст перевода, что может привести к несправедливой оценке переводов.


### 3.2 ROUGE

ROUGE метрика вычисляется на основе пересечения (overlap) между n-граммами (конкретно, униграммами, биграммами и т. д.) в сгенерированном суммаризированном тексте и эталонном суммаризированном тексте. Она имеет несколько вариантов, таких как ROUGE-N, ROUGE-L и ROUGE-W.

ROUGE-N: Вычисляет пересечение униграмм, биграмм, триграмм и т.д. между сгенерированным и эталонным текстами.

ROUGE-L: Вычисляет самую длинную общую подпоследовательность (longest common subsequence) между сгенерированным и эталонным текстами.

ROUGE-W: Вычисляет пересечение между взвешенными n-граммами с учетом позиции слов в предложении.

Формула расчета метрики ROUGE-N выглядит следующим образом:
```math
ROUGE_N = {\sum_r \sum_n count_m (Ngram) \over \sum_r \sum_n count_t (Ngram)}
```
$N$ - максимальная длина n-граммы (обычно используется до 4).

$count_m (Ngram)$ - количество n-грамм, которые встречаются в сгенерированном тексте и в эталонных текстах.

$count_t (Ngram)$ - общее количество n-грамм в эталонных текстах.

Формула для ROUGE-L использует самую длинную общую подпоследовательность между сгенерированным и эталонным текстами, а формула для ROUGE-W учитывает весовые коэффициенты для позиции слов в предложениях.

### 3.3 BERTScore
BERTScore - это метрика для оценки качества генерации текста, основанная на использовании предобученной модели BERT (Bidirectional Encoder Representations from Transformers). Она предназначена для оценки сходства между сгенерированным текстом и эталонным текстом с использованием векторных представлений слов, полученных с помощью BERT.

Формула расчета:
BERTScore вычисляется на основе косинусного расстояния между эмбеддингами слов в сгенерированном и эталонном текстах:

```math
BERTScore = {cos(s_i, g) \over N}
```
$s_i$ - векторное представление слова 

$g$ - векторное представление слова $i$ в эталонном тексте.
$N$ - общее количество слов в сгенерированном тексте.

Для чего используется:

BERTScore используется для оценки качества генерации текста, например, в задачах машинного перевода, субтитров и генерации текста на естественном языке. Она позволяет оценить степень семантического сходства между сгенерированным и эталонным текстами на основе их векторных представлений.

Интерпретация:
Чем выше значение BERTScore, тем более сходными являются сгенерированный и эталонный тексты. Максимальное значение BERTScore равно 1, что означает идентичность между сгенерированным и эталонным текстами.

Плюсы:

+ BERTScore использует предобученную модель BERT, которая обучена на больших объемах текстовых данных и демонстрирует высокую производительность в различных задачах обработки естественного языка.
+ Она предоставляет более точную оценку качества генерации текста по сравнению с классическими метриками, такими как BLEU или ROUGE.
+ BERTScore учитывает семантическое сходство между сгенерированным и эталонным текстами, что делает ее более информативной и полезной в реальных приложениях.

Минусы:

- BERTScore требует предварительно обученной модели BERT, что может потребовать больших вычислительных ресурсов для ее применения.
- Она может быть более медленной в вычислении по сравнению с классическими метриками, так как включает в себя вычисление векторных представлений слов с помощью BERT.
- BERTScore может быть менее интерпретируемой, чем некоторые другие метрики, так как она основана на сложных моделях глубокого обучения.

### 3.4 Семантическая близость на основе Sentence Transformers

Семантическое сходство текстов - это метрика, которая оценивает насколько похожи два текста с точки зрения смысла. 
Ответы модели и эталонные значения кодируются в эмбеддинги с помощью одной из предварительно обученных моделей "all-MiniLM-L6-v2" SentenceTransformer. 

Затем оценивается косинусное сходство ответа и эталонного значения 
Косинусное сходсттво - это мера сходства между двумя ненулевыми векторами, определенными в пространстве внутреннего произведения.
Косинусное сходство принадлежит интервалу [0,1] - два пропорциональных вектора имеют косинусное сходство, равное 1, а два ортогональных вектора имеют сходство, равное 0.

