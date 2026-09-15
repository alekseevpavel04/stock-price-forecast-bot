# stock-price-forecast-bot

HSE course project (2023–2024). A Telegram bot that forecasts stock prices with an ML model (gradient boosting) and a DL model (LSTM), and also shows recent trading data, company information and analyst recommendations. The models run in a separate FastAPI service; user requests are logged to PostgreSQL.

Author: Pavel Alekseev (Telegram @pavel0420). Curator: Tatiana Fofanova (@tfofanova).

![example](https://github.com/alekseevpavel04/project/assets/48567496/62c323da-90a8-41ca-8704-83787ac4557f)

## Features

- `/predict_ml <TICKER>`: forecast with the pre-trained gradient boosting model
- `/predict_dl <TICKER>`: forecast with the pre-trained LSTM model
- `/last`, `/info`, `/recom`: latest trades, company information, analyst recommendations (via yfinance)
- `/base`: request statistics stored in PostgreSQL
- `/start`, `/help`

## How it works

- **Data:** daily prices downloaded with `yfinance`; Box-Cox transform (λ stored in `lambda_val.pkl`), lagged and rolling-window features, MinMax scaling.
- **ML model:** gradient boosting (`GB_model.pkl`), selected in `notebooks/ML_model_analysis.ipynb`, where gradient boosting and random forest were trained and tuned.
- **DL model:** PyTorch LSTM (5 layers, hidden size 64) in `trained_model_lstm.pth`, trained in `notebooks/DL_model_analysis.ipynb`.
- **Services:** FastAPI app with `POST /predict_ml` and `POST /predict_dl`; aiogram 3 Telegram bot; PostgreSQL; all started with Docker Compose.

## Tech stack

Python, pandas, scikit-learn, LightGBM, PyTorch, yfinance, FastAPI, aiogram, SQLAlchemy, PostgreSQL, Docker Compose, pytest.

## Project structure

```
├── fastapi_app/                 # FastAPI service with the models
│   ├── Dockerfile
│   ├── fastapi_app.py
│   ├── model_data/
│   │   ├── GB_model.pkl         # gradient boosting model
│   │   ├── lambda_val.pkl       # Box-Cox lambda
│   │   └── trained_model_lstm.pth  # LSTM weights
│   ├── requirements.txt
│   └── test_fastapi_app.py
├── telegram_bot/                # Telegram bot
│   ├── Dockerfile
│   ├── bot.py
│   ├── requirements.txt
│   └── test_bot.py
├── notebooks/
│   ├── EDA.ipynb                # exploratory data analysis
│   ├── ML_model_analysis.ipynb  # ML model training
│   └── DL_model_analysis.ipynb  # LSTM training
├── presentations/
│   └── ML_model_analysis.pdf
├── docker-compose.yaml
└── LICENSE
```

## How to run

```bash
git clone https://github.com/alekseevpavel04/stock-price-forecast-bot.git
cd stock-price-forecast-bot
echo "TELEGRAM_TOKEN=YOUR_TG_TOKEN" > .env
docker-compose up
```

## Tests

- FastAPI service: run `pytest` from `fastapi_app/`.
- Telegram bot: run `pytest -s` from `telegram_bot/`. The bot tests log in to a Telegram test account (they ask for a login and code) and require the whole project to be running (`docker-compose up`). Install the requirements in a virtual environment first.

## License

MIT, see [LICENSE](LICENSE).

## Кратко по-русски

Учебный проект ВШЭ (2023–2024): Telegram-бот для прогнозирования цен акций. Прогноз строится ML-моделью (градиентный бустинг) или DL-моделью (LSTM) в отдельном FastAPI-сервисе. Бот также показывает последние торги, информацию о компании и рекомендации аналитиков. Запуск: `docker-compose up` с `TELEGRAM_TOKEN` в `.env`.
