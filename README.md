# 🎬 Рекомендатор фильмов по настроению

Веб-приложение на **Flask**, которое анализирует эмоциональную окраску вашего текста и с помощью нейросети рекомендует подходящий фильм.

## ✨ Возможности
- 🔍 **Анализ настроения**: определяет позитивный, негативный или нейтральный тон текста с помощью `rubert-base-cased-sentiment`.
- 🤖 **Генерация рекомендации**: подбирает фильм через `rugpt3medium_based_on_gpt2`, учитывая выявленное настроение.
- 🌐 **Простой веб-интерфейс**: отправка текста и мгновенный ответ через стандартный POST-запрос.
- 🛡️ **Устойчивость к выводам модели**: встроенный парсинг и фоллбэки для извлечения названия фильма из сгенерированного текста.

## 📦 Требования
- Python 3.8+
- `flask`
- `transformers`
- `torch`
- ~2 ГБ свободного места для кэширования моделей (загружаются автоматически при первом запуске)
- 💡 **Рекомендуется GPU** для ускорения генерации. На CPU один запрос может занимать 10–30 секунд.

## 🚀 Установка и запуск

1. Создайте папку проекта и перейдите в неё:
   ```bash
   mkdir movie-recommender && cd movie-recommender
   ```

2. Создайте виртуальное окружение и установите зависимости:
   ```bash
   python -m venv venv
   source venv/bin/activate   # Windows: venv\Scripts\activate
   pip install flask transformers torch
   ```

3. Создайте структуру файлов:
   ```
   movie-recommender/
   ├── app.py
   └── templates/
       └── index.html
   ```

4. Поместите ваш код в `app.py`, а минимальный шаблон HTML создайте в `templates/index.html` (пример ниже).

5. Запустите приложение:
   ```bash
   python app.py
   ```

6. Откройте браузер и перейдите по адресу: [http://127.0.0.1:5000](http://127.0.0.1:5000)

## 📝 Пример `templates/index.html`
```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Кино по настроению</title>
    <style>
        body { font-family: system-ui, sans-serif; max-width: 600px; margin: 2rem auto; padding: 0 1rem; background: #fafafa; }
        form { display: flex; flex-direction: column; gap: 0.5rem; }
        textarea { padding: 0.8rem; border: 1px solid #ccc; border-radius: 6px; resize: vertical; }
        button { padding: 0.6rem; background: #2563eb; color: white; border: none; border-radius: 6px; cursor: pointer; }
        button:hover { background: #1d4ed8; }
        .result { margin-top: 1.5rem; padding: 1rem; background: #fff; border: 1px solid #e5e7eb; border-radius: 8px; line-height: 1.5; }
    </style>
</head>
<body>
    <h1>🎬 Подбери фильм по настроению</h1>
    <form method="POST">
        <textarea name="message" placeholder="Напишите, как вы себя чувствуете или что думаете..." required>{{ user_text }}</textarea>
        <button type="submit">Получить рекомендацию</button>
    </form>
    {% if recommendation %}
        <div class="result">{{ recommendation | safe }}</div>
    {% endif %}
</body>
</html>
```

## ⚙️ Как это работает
1. Пользователь вводит текст и отправляет форму.
2. Текст передаётся пайплайну `sentiment-analysis` для классификации тональности.
3. На основе метки формируется промпт для RuGPT-3.
4. Модель генерирует ответ, из которого с помощью регулярного выражения и резервного метода извлекается название фильма.
5. Результат рендерится в шаблоне и возвращается пользователю.

## ⚠️ Важные заметки
- 🔧 **Regex-опечатка**: в оригинальном коде строка `re.search(r'«([^»]{2,100}»', generated_text)` содержит синтаксическую ошибку (пропущена `]`). Рекомендуется заменить на:
  ```python
  re.search(r'«([^»]{2,100})»', generated_text)
  ```
- 🌐 При первом запуске модели загружаются с серверов Hugging Face. Требуется стабильное интернет-соединение.
- 🚀 `debug=True` включён только для локальной разработки. Для продакшена используйте `debug=False` и WSGI-сервер (Gunicorn, Waitress или uWSGI).
- 📉 Если генерация слишком медленная, можно переключиться на более лёгкие модели (например, `distilgpt2` или квантованные версии через `bitsandbytes`).

## 📄 Лицензия
MIT (или укажите нужную)

---
💡 *Хотите улучшить точность рекомендаций? Попробуйте добавить few-shot примеры в промпт или использовать специализированные датасеты фильмов вместо генеративной модели.*
