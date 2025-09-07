# Предсказание токсичности химических соединений (RandomForest)

Учебный проект по предсказанию токсичности соединений на основе их химической структуры (SMILES). 
В проекте используется конвейер: **EDA → RDKit-дескрипторы → RandomForest → RMSE → Инференс на тесте**.

> ✨ Проект оформлен как учебный: подробные Markdown-пояснения, функции с **аннотациями типов** и **докстрингами**, комментарии в «студенческом» стиле и блок **«Итоги и рекомендации»**.

---

## 📁 Структура репозитория

```
.
├── toxicity_rf_project.ipynb    # Основной ноутбук с полным пайплайном
├── train.csv                    # Обучающие данные (SMILES, activity)
├── test_only_smiles.csv         # Тестовые данные (только SMILES)
└── predictions.csv              # Результаты предсказаний (будет создан после запуска ноутбука)
```

> Если вы используете Google Colab, загрузите файлы `train.csv` и `test_only_smiles.csv` рядом с ноутбуком или смонтируйте Google Drive.

---

## 🔬 Задача

- **Вход:** SMILES-представления молекул.
- **Цель:** численная оценка токсичности `activity` (регрессия).
- **Метрика:** RMSE (Root Mean Squared Error).

---

## 🧰 Технологии и библиотеки

- Python 3.9+
- [pandas](https://pandas.pydata.org/), [numpy](https://numpy.org/)
- [matplotlib](https://matplotlib.org/), [seaborn](https://seaborn.pydata.org/)
- [scikit-learn](https://scikit-learn.org/) — `RandomForestRegressor`
- [RDKit](https://www.rdkit.org/) — генерация молекулярных дескрипторов из SMILES

---

## 🧪 Что внутри ноутбука

1. **EDA (анализ данных)**: структура, пропуски, дубликаты, распределения (`activity` и длины SMILES), boxplot и краткие выводы.  
2. **Feature engineering (RDKit)**: вычисление 13 дескрипторов на молекулу:
   - `MolWt`, `MolLogP` (гидрофобность), `HeavyAtomCount`, `FractionCSP3`, `NOCount`,
   - `NumHAcceptors`, `NumHDonors`, `NumHeteroatoms`, `NumRotatableBonds`,
   - `RingCount`, `NumAromaticRings`, `NumAliphaticRings`, `NumSaturatedRings`.
3. **Обучение модели**: простое разбиение `train/val` (80/20), **RandomForestRegressor(n_estimators=200)**.
4. **Оценка**: RMSE на train/val, важности признаков (bar plot).
5. **Инференс**: дообучение на всём train и предсказания для `test_only_smiles.csv` → `predictions.csv`.

---

## 🚀 Как запустить

### Вариант A: Google Colab
1. Загрузите в Colab файл `toxicity_rf_project.ipynb`.
2. Загрузите `train.csv` и `test_only_smiles.csv` в ту же рабочую директорию (либо смонтируйте Google Drive).
3. В первой кодовой ячейке раскомментируйте установку зависимостей:
   ```python
   # !pip -q install rdkit-pypi
   # !pip -q install pandas scikit-learn matplotlib seaborn
   ```
4. Запустите все ячейки сверху вниз (`Runtime` → `Run all`).
5. После выполнения появится файл `predictions.csv` (его можно скачать из файлового менеджера Colab).

### Вариант B: Локально (conda)
```bash
conda create -n toxenv python=3.10 -y
conda activate toxenv
pip install pandas scikit-learn matplotlib seaborn rdkit-pypi
pip install notebook
jupyter notebook toxicity_rf_project.ipynb
```
> Убедитесь, что рядом лежат `train.csv` и `test_only_smiles.csv`.

---

## 📊 Результаты и метрика

- **Метрика:** RMSE на отложенной валидации (hold-out 20%).  
- **Важно:** точное значение RMSE зависит от конкретных данных и случайного разбиения. Посмотрите вывод ячейки с оценкой в ноутбуке после запуска у вас.

Пример отчёта в ноутбуке включает:
- RMSE на train и val
- Диаграмму важности признаков
- Короткие интерпретации (какие признаки значимее и почему)

---

## 🧠 Пояснения и учебные акценты

- Все функции снабжены **аннотациями типов** и **докстрингами**.
- В EDA отмечаются возможные аномалии (длинные SMILES, отрицательные activity и т.п.).
- Обсуждается, почему **масштабирование признаков** не обязательно для деревьев.
- Есть блок **«Итоги и рекомендации»**: идеи по улучшению (fingerprints, бустинги, кросс-валидация и др.).

---

## 🧪 Репликация эксперимента

1. Убедитесь, что у вас те же файлы `train.csv` и `test_only_smiles.csv`.
2. Запустите ноутбук целиком (локально или в Colab) без изменения кода.
3. Сравните RMSE и графики с показанными в ноутбуке.

---

## 📈 Возможные улучшения

- Добавить молекулярные **fingerprints** (напр., Morgan), объединить с RDKit-дескрипторами.
- Тюнинг гиперпараметров (глубина деревьев, число деревьев, min_samples_leaf).
- Использовать градиентный бустинг (CatBoost/LightGBM/XGBoost).
- Кросс-валидация (KFold/StratifiedKFold) для стабильной оценки.
- Модели по самим SMILES (RNN/Transformer) — если это релевантно курсу.

---

## 🧾 Лицензия

MIT — используйте и модифицируйте для учебных целей. При публикации в открытом доступе, пожалуйста, оставьте ссылку на этот репозиторий/автора.

---

## 🙌 Благодарности

- RDKit — за мощные химоинформатические инструменты.
- Сообщество open-source библиотек Python за удобный стек для научных вычислений.
