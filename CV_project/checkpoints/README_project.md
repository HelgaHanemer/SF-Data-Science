
# Классификация рентгеновских снимков лёгких (Normal / COVID / Viral Pneumonia)

Проект решает задачу 3-классовой классификации рентгеновских снимков грудной клетки:
- **Normal**
- **COVID**
- **Viral Pneumonia**

Основной артефакт — Jupyter-тетрадь с полностью воспроизводимым пайплайном:  
**`COVID_CXR_3class_classifier_PEP8.ipynb`** (PEP-8, аннотации типов, докстринги, выбор лучшей модели по валидации, финальная оценка на тесте, экспорт предсказаний).

---

## 🔧 Функциональность

- Стратифицированное разбиение данных на **train / val / test** (70/15/15).
- Кастомный **PyTorch Dataset** и корректные **DataLoader’ы** (shuffle только в train).
- Модель по умолчанию: **EfficientNet-B0** (transfer learning).
- Веса классов (class weights) для компенсации дисбаланса.
- Циклы **обучения и валидации** с логированием `loss` и `macro-F1`.
- **Выбор лучшей модели по валидации** (сохранение `best.pt`), затем — оценка на тесте.
- **Метрики** по каждому классу и усреднённые (**precision / recall / F1**, `macro avg`).
- Экспорт предсказаний теста в `checkpoints/test_predictions.csv`.
- Пресеты: **быстрый** (smoke-test) и **боевой** (для лучшего качества).

---

## 🗂 Структура данных (требуемая)

```
data/
 ├─ Normal/
 ├─ COVID/
 └─ Viral Pneumonia/
```
> Внутри — изображения (`.png/.jpg/.jpeg/.bmp`). Скрытые файлы игнорируются.

---

## 🚀 Как запустить

### Вариант A — положить данные рядом с тетрадью (рекомендуется)
```
project/
 ├─ COVID_CXR_3class_classifier_PEP8.ipynb
 └─ data/
     ├─ Normal/
     ├─ COVID/
     └─ Viral Pneumonia/
```
Тетрадь автоматически найдёт `./data`.

### Вариант B — указать путь к датасету через переменную окружения
В начале тетради выполните:
```python
import os
os.environ["DATA_ROOT"] = "C:/path/to/dataset"  # Windows
# os.environ["DATA_ROOT"] = "/home/user/dataset"  # Linux/macOS
```

---

## 💻 Зависимости
```bash
pip install torch torchvision torchaudio  # выберите CPU/свои CUDA-билды на pytorch.org
pip install scikit-learn pillow matplotlib tqdm ipywidgets==8.* widgetsnbextension
# для классического Jupyter: jupyter nbextension enable --py widgetsnbextension
```

---

## ⚙️ Пресеты (встроены в тетрадь)
```python
def use_quick_preset():
    # Быстрый smoke-test
    CFG.img_size = 192
    CFG.batch_size = 32
    CFG.num_workers = 0
    CFG.epochs = 2

def use_full_preset():
    # Боевое качество
    CFG.img_size = 224
    CFG.batch_size = 32
    CFG.num_workers = 2
    CFG.epochs = 15
```
По умолчанию активирован **быстрый пресет**. Для «боевого» вызовите `use_full_preset()`.

---

## 🧪 Результаты прогона (быстрый пресет)

**Конфигурация:** EfficientNet-B0, `img_size=192`, `epochs=2`, `batch_size=32`, `num_workers=0`.  
**Валидация по эпохам:**
- Эпоха 1 — **Train:** loss=0.2013, macroF1=0.8876 | **Val:** loss=0.0838, macroF1=0.9416  
- Эпоха 2 — **Train:** loss=0.0685, macroF1=0.9598 | **Val:** loss=0.0595, macroF1=0.9753  
**Сохранён лучший чекпоинт:** `checkpoints/best.pt` (по валидации).

**Тест:** **macro avg F1 = 0.9798**  
(Отчёт по классам и confusion matrix — см. раздел 9 тетради; предсказания: `checkpoints/test_predictions.csv`).

**Краткий вывод:** переобучения не наблюдается (кривые train/val согласованы, разрыв не растёт). Результат устойчивый для быстрого режима; для подтверждения — запустить «боевой» пресет и/или k-fold CV.

---

## ✅ Соответствие критериям

1) Разбиение **train/val/test** со стратификацией — раздел 2 тетради.  
2) **PyTorch Dataset** — раздел 3 (`CXRDataset`).  
3) **DataLoader’ы** — shuffle **только** в train — раздел 4.  
4) **Циклы обучения/валидации** — валидация **в каждой эпохе** — раздел 7.  
5) **Модель обучена** — разделы 5–7.  
6) **Выбор лучшей модели по валидации** (сохранение `best.pt`, не по тесту) — раздел 7.  
7) **Метрики на тесте** — per-class + macro avg (precision/recall/F1) — раздел 9.  
8) Цель **macro avg F1 ≥ 0.8** — достигнута (0.9798).

---

## 📌 Рекомендации по повышению качества

- Увеличить `img_size` до 224–256 и `epochs` до 15–25 (с **early stopping**).
- Усилить аугментации: `RandomResizedCrop`, лёгкий `ColorJitter`, `GaussianBlur`.
- Проверить данные на «шорткаты» (надписи *R/L*, даты, водяные знаки) и баланс классов.
- При наличии ID пациентов — использовать **patient-level split**.
- Прогнать с разными `SEED`, сделать **5-fold CV**.
