import yfinance as yf
import pandas as pd
import numpy as np
from datetime import date
import statsmodels.api as sm

def get_returns(ticker, period=None, start=None, end=None):
    """Retorna série de preços ajustados e o retorno total."""
    tk = yf.Ticker(ticker)
    if period:
        df = tk.history(period=period)
    else:
        df = tk.history(start=start, end=end)
    prices = df["Close"]
    total_return = prices.iloc[-1] / prices.iloc[0] - 1
    return prices, total_return

def compute_alpha_beta(r_asset, r_spx):
    """
    Faz regressão r_asset ~ alpha + beta * r_spx
    Retorna (alpha, beta).
    """
    # alinhar índices
    df = pd.concat([r_asset, r_spx], axis=1).dropna()
    y = df.iloc[:,0]
    X = sm.add_constant(df.iloc[:,1])
    model = sm.OLS(y, X).fit()
    return model.params["const"], model.params[df.columns[1]]

def compare_to_spx(ticker):
    spx_ticker = "^GSPC"
    today = date.today().isoformat()
    periods = {
        "5y": {"period":"5y"},
        "1y": {"period":"1y"},
        "YTD": {"start": f"{today[:4]}-01-01", "end": today},
        "3mo": {"period":"3mo"},
        "1mo": {"period":"1mo"},
        "1w":  {"period":"7d"},
        "1d":  {"period":"1d"},
    }
    results = []

    # coleta série diária pra regressão
    prices_asset, _ = get_returns(ticker, period="5y")
    prices_spx,   _ = get_returns(spx_ticker, period="5y")
    # retorna série de retornos diários
    r_asset = prices_asset.pct_change().dropna()
    r_spx   = prices_spx.pct_change().dropna()
    alpha, beta = compute_alpha_beta(r_asset, r_spx)

    for name, opts in periods.items():
        pa, ra = get_returns(ticker, **opts)
        ps, rs = get_returns(spx_ticker, **opts)
        delta = ra - rs
        results.append({
            "Periodo": name,
            "Retorno_Ativo": ra,
            "Retorno_SPX":  rs,
            "Delta":        delta
        })

    df = pd.DataFrame(results).set_index("Periodo")
    df["Alpha"] = alpha   # fixa alfa calculado sobre 5 anos
    df["Beta"]  = beta
    return df

if __name__ == "__main__":
    ticker = input("Informe o ticker da ação: ").strip().upper()
    df = compare_to_spx(ticker)
    print(df.to_markdown(floatfmt=".2%"))
