# Учебный проект: предсказание токсичности соединений (RandomForest)

## Описание проекта
Цель — по строкам **SMILES** предсказать числовую токсичность `activity`. 
Проект оформлен в одном ноутбуке: анализ данных (EDA), генерация признаков из RDKit, обучение модели RandomForest и инференс на тесте.

## Что использовали
- **Данные:** `train.csv` (SMILES, activity) и `test_only_smiles.csv` (SMILES).
- **Инструменты:** Python, `pandas`, `numpy`, `matplotlib`, `scikit-learn`, **RDKit**.
- **Среда:** Google Colab / локально (conda/venv).

## Методы
1. **EDA:** размеры, пропуски/дубликаты, распределение `activity`, длина SMILES; 
   корреляции с целью (bar), **тепловая карта корреляций**; 
   доп. графики: гистограммы `MolWt`, `MolLogP`, scatter `MolWt/MolLogP/NumAromaticRings vs activity`, 
   boxplot `activity` по квинтилям `MolWt`. Под каждым графиком — краткий вывод.
2. **Признаки (RDKit):** 13 дескрипторов на молекулу: `MolWt`, `MolLogP`, `HeavyAtomCount`, `FractionCSP3`, 
   `NOCount`, `NumHAcceptors/Donors`, `NumHeteroatoms`, `NumRotatableBonds`, `RingCount`, 
   `NumAromaticRings/AliphaticRings/SaturatedRings`.
3. **Модель:** `RandomForestRegressor (n_estimators=200)`, простое разбиение `train/val = 80/20`, метрика **RMSE**.
4. **Инференс:** дообучение на всём train и предсказания для теста → `predictions.csv`.

## Результаты (кратко)
- **RMSE (val):** зависит от конкретного разбиения; смотреть вывод ноутбука.
- **Важность признаков:** вклад в основном от **массы (MolWt)**, **гидрофобности (MolLogP)** и **структурных индикаторов** (FractionCSP3, кольца).
- **Наблюдения из EDA:** токсичность чаще выше у более тяжёлых и ароматических молекул; у более насыщенных (высокий `FractionCSP3`) ниже.

## Выводы
- Базовый пайплайн RDKit-дескрипторы → RandomForest даёт разумное качество и понятную интерпретацию.
- Для улучшения: добавить молекулярные fingerprints (например, Morgan), настроить гиперпараметры, 
  попробовать бустинги/кросс-валидацию или модели по самим SMILES.
