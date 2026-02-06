import streamlit as st

st.title("Safe Trading Risk Calculator")

account = st.number_input("Account size ($)", min_value=1.0, value=300.0, step=10.0)
daily_pct = st.number_input("Daily max loss (%)", min_value=0.1, value=1.5, step=0.1) / 100
risk_pct  = st.number_input("Risk per trade (%)", min_value=0.1, value=0.5, step=0.1) / 100
max_trades = st.number_input("Max trades/day", min_value=1, value=2, step=1)

entry = st.number_input("Entry price ($)", min_value=0.0000001, value=1.25, step=0.01, format="%.6f")
stop_pct = st.number_input("Stop distance (%)", min_value=0.1, value=0.8, step=0.1) / 100
direction = st.selectbox("Direction", ["long", "short"])
target_r = st.number_input("Target (R multiple)", min_value=0.5, value=2.0, step=0.5)

daily_max_loss = account * daily_pct
risk_per_trade = account * risk_pct
stop_dist = entry * stop_pct

position_dollars = risk_per_trade * entry / stop_dist
qty = position_dollars / entry

stop_price = entry + stop_dist if direction == "short" else entry - stop_dist
target_price = entry - stop_dist * target_r if direction == "short" else entry + stop_dist * target_r

st.subheader("Results")
st.write(f"**Daily max loss:** ${daily_max_loss:.2f}")
st.write(f"**Risk per trade:** ${risk_per_trade:.2f}")
st.write(f"**Position size:** ${position_dollars:.2f}")
st.write(f"**Quantity:** {qty:.6f}")
st.write(f"**Stop price ({direction}):** ${stop_price:.6f}")
st.write(f"**Target (~{target_r}R):** ${target_price:.6f}")

st.caption("Rule: stop trading if daily P&L hits daily max loss, or after 2 losses.")
