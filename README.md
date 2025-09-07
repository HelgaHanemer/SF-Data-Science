# Учебный проект: предсказание токсичности (RandomForest)

Простой учебный проект: по SMILES хотим предсказать числовую токсичность `activity`.
Работа оформлена как ноутбук с EDA, фичами из RDKit и моделью RandomForest.

## Что есть в репозитории
- `toxicity_rf_project.ipynb` — основной ноутбук (все шаги).
- `train.csv` — обучающие данные (SMILES, activity) — положите рядом с ноутбуком.
- `test_only_smiles.csv` — тестовые данные (только SMILES) — положите рядом.
- `predictions.csv` — появится после запуска ноутбука (предсказания для теста).
- `README.md` — короткое описание (этот файл).

## Как запустить (Colab)
1. Откройте `toxicity_rf_project.ipynb` в Google Colab.
2. Загрузите `train.csv` и `test_only_smiles.csv` в ту же папку.
3. В первой ячейке раскомментируйте установку и выполните:
   ```python
   # !pip -q install rdkit pandas scikit-learn matplotlib
   ```
4. Запустите все ячейки сверху вниз. В конце появится `predictions.csv`.

## Как запустить (локально)
Вариант через conda:
```bash
conda create -n toxenv python=3.10 -y
conda activate toxenv
pip install rdkit pandas scikit-learn matplotlib notebook
jupyter notebook toxicity_rf_project.ipynb
```
(Файлы `train.csv` и `test_only_smiles.csv` должны лежать рядом.)

## Что делает ноутбук (кратко)
- **EDA**: размеры, пропуски/дубликаты, распределения, корреляции, тепловая карта.
- **Признаки (RDKit)**: 13 простых дескрипторов (MolWt, MolLogP, кольца и т.п.).
- **Модель**: RandomForestRegressor (простое train/val-разбиение 80/20), метрика RMSE.
- **Финал**: дообучение на всём train и предсказания для теста → `predictions.csv`.
- Под каждым графиком есть короткий **«Вывод»** (1–2 предложения).

## Что посмотреть при проверке
- RMSE на валидации (ячейка с метрикой).
- Бар с важностью признаков.
- Пару графиков из EDA (распределения и scatter vs activity) + выводы.

## Частые проблемы
- `ModuleNotFoundError: rdkit` → не то окружение. В Colab поставьте `pip install rdkit` и перезапустите runtime; локально выберите правильный интерпретатор.
- Папка пуста на GitHub → в Git нельзя пустые папки, добавьте файл `.gitkeep` или `README.md`.

## Лицензия
MIT. Можно использовать в учебных целях.
