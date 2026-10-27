# Electric-Vehicle-Market-Performance-Insights
Overview 
The Electric Vehicle Market Performance Insights project focuses on analyzing the performance and growth of the electric vehicle (EV) market using data visualization in Tableau.
The main objective of this project is to understand the growth of electric vehicles, compare different vehicle categories, analyze market performance, and identify trends in EV adoption over time.
Using Tableau, the data is represented through different charts and graphs that make it easier to understand important information, such as vehicle sales, market share, geographical distribution, and year-wise growth.
This dashboard helps users identify market trends, understand the performance of electric vehicles, and gain useful insights into the future potential of the EV industry.
Tools used: Tableau Public, data visualization, and data analysis.

Code for the Dashboard
import streamlit as st
import pandas as pd
import plotly.express as px

st.set_page_config(
    page_title="EV Market Performance and Insights",
    page_icon="🚗",
    layout="wide"
)

st.title("🚗 Electric Vehicle Market Performance and Insights")
st.markdown(
    "Analyze EV sales, manufacturers, customer ratings, "
    "battery types, vehicle range, and regional performance."
)

# -----------------------------------
# 1. Sample dataset
# -----------------------------------

data = {
    "Manufacturer": [
        "Tata Motors", "MG", "Mahindra", "Hyundai",
        "Tesla", "BYD", "BMW", "Kia",
        "Tata Motors", "MG", "Mahindra", "Hyundai",
        "Tesla", "BYD", "BMW", "Kia"
    ],
    "Vehicle Category": [
        "Compact SUV", "Sedan", "SUV", "Hatchback",
        "Luxury Sedan", "SUV", "Luxury Sedan", "Compact SUV",
        "E-Bus", "E-Truck", "3-Wheeler", "Crossover",
        "Full SUV", "Pickup", "Sports Car", "Van/MPV"
    ],
    "Market Region": [
        "South Asia", "Europe", "South Asia", "Europe",
        "North America", "Asia Pacific", "Europe", "Asia Pacific",
        "South Asia", "North America", "South Asia", "Europe",
        "North America", "Asia Pacific", "Europe", "Middle East"
    ],
    "Battery Type": [
        "LFP", "NMC", "LFP", "NCA",
        "NCA", "LFP", "NMC", "NMC",
        "Solid-State", "LFP", "Sodium-Ion", "NMC",
        "NCA", "LFP", "Solid-State", "Sodium-Ion"
    ],
    "Annual Sales Units": [
        850000, 620000, 710000, 540000,
        950000, 1200000, 460000, 390000,
        120000, 85000, 160000, 480000,
        780000, 190000, 110000, 250000
    ],
    "Customer Rating": [
        4.2, 4.0, 4.1, 3.9,
        4.3, 4.4, 4.2, 4.0,
        3.8, 3.7, 4.1, 4.0,
        4.2, 3.9, 4.1, 3.8
    ],
    "Range Km": [
        450, 500, 480, 350,
        650, 600, 550, 420,
        250, 300, 180, 520,
        620, 400, 580, 380
    ],
    "Latitude": [
        22.3, 51.5, 19.1, 37.6,
        37.4, 23.1, 48.1, 37.5,
        28.6, 40.7, 22.7, 48.8,
        30.3, 31.2, 45.5, 25.2
    ],
    "Longitude": [
        73.2, -0.1, 72.9, 127.0,
        -122.1, 113.3, 11.6, 127.0,
        77.2, -74.0, 75.9, 2.3,
        -97.7, 121.5, 9.2, 55.3
    ]
}

df = pd.DataFrame(data)

# -----------------------------------
# 2. Sidebar filters
# -----------------------------------

st.sidebar.header("Dashboard Filters")

manufacturers = st.sidebar.multiselect(
    "Select Manufacturer",
    options=sorted(df["Manufacturer"].unique()),
    default=sorted(df["Manufacturer"].unique())
)

categories = st.sidebar.multiselect(
    "Select Vehicle Category",
    options=sorted(df["Vehicle Category"].unique()),
    default=sorted(df["Vehicle Category"].unique())
)

regions = st.sidebar.multiselect(
    "Select Market Region",
    options=sorted(df["Market Region"].unique()),
    default=sorted(df["Market Region"].unique())
)

filtered_df = df[
    df["Manufacturer"].isin(manufacturers)
    & df["Vehicle Category"].isin(categories)
    & df["Market Region"].isin(regions)
]

if filtered_df.empty:
    st.warning("No data matches your filters.")
    st.stop()

# -----------------------------------
# 3. Summary metrics
# -----------------------------------

total_sales = filtered_df["Annual Sales Units"].sum()
avg_rating = filtered_df["Customer Rating"].mean()
avg_range = filtered_df["Range Km"].mean()
total_manufacturers = filtered_df["Manufacturer"].nunique()

c1, c2, c3, c4 = st.columns(4)

c1.metric("Total Sales Units", f"{total_sales:,.0f}")
c2.metric("Average Rating", f"{avg_rating:.2f}/5")
c3.metric("Average Range", f"{avg_range:.0f} km")
c4.metric("Manufacturers", total_manufacturers)

st.divider()

# -----------------------------------
# 4. Vehicle Sales by Category
# -----------------------------------

st.subheader("Vehicle Sales by Category")

category_sales = (
    filtered_df.groupby("Vehicle Category", as_index=False)
    ["Annual Sales Units"].sum()
    .sort_values("Annual Sales Units")
)

fig1 = px.bar(
    category_sales,
    x="Annual Sales Units",
    y="Vehicle Category",
    orientation="h",
    title="Vehicle Sales by Category",
    color="Annual Sales Units",
    color_continuous_scale="Blues"
)

# -----------------------------------
# 5. Manufacturer Sales
# -----------------------------------

manufacturer_sales = (
    filtered_df.groupby("Manufacturer", as_index=False)
    ["Annual Sales Units"].sum()
    .sort_values("Annual Sales Units", ascending=False)
)

fig2 = px.bar(
    manufacturer_sales,
    x="Annual Sales Units",
    y="Manufacturer",
    orientation="h",
    title="Manufacturer Sales",
    color="Annual Sales Units",
    color_continuous_scale="Teal"
)

fig2.update_layout(yaxis={"categoryorder": "total ascending"})

# -----------------------------------
# 6. Customer Rating
# -----------------------------------

ratings = (
    filtered_df.groupby("Manufacturer", as_index=False)
    ["Customer Rating"].mean()
    .sort_values("Customer Rating")
)

fig3 = px.bar(
    ratings,
    x="Customer Rating",
    y="Manufacturer",
    orientation="h",
    title="Customer Rating by Manufacturer",
    range_x=[0, 5],
    color="Customer Rating",
    color_continuous_scale="Blues"
)

# -----------------------------------
# 7. Regional Sales
# -----------------------------------

regional_sales = (
    filtered_df.groupby("Market Region", as_index=False)
    ["Annual Sales Units"].sum()
    .sort_values("Annual Sales Units")
)

fig4 = px.bar(
    regional_sales,
    x="Annual Sales Units",
    y="Market Region",
    orientation="h",
    title="Regional Sales",
    color="Annual Sales Units",
    color_continuous_scale="Blues"
)

# -----------------------------------
# 8. EV Sales by Battery Type
# -----------------------------------

battery_sales = (
    filtered_df.groupby("Battery Type", as_index=False)
    ["Annual Sales Units"].sum()
    .sort_values("Annual Sales Units")
)

fig5 = px.bar(
    battery_sales,
    x="Annual Sales Units",
    y="Battery Type",
    orientation="h",
    title="EV Sales by Battery Type",
    color="Annual Sales Units",
    color_continuous_scale="Teal"
)

# -----------------------------------
# 9. Range Category
# -----------------------------------

range_data = filtered_df.copy()

range_data["Range Category"] = pd.cut(
    range_data["Range Km"],
    bins=[0, 250, 400, 500, 700],
    labels=[
        "Short Range",
        "Medium Range",
        "Long Range",
        "Extended Range"
    ],
    include_lowest=True
)

range_sales = (
    range_data.groupby(
        "Range Category", observed=False, as_index=False
    )["Annual Sales Units"].sum()
)

fig6 = px.bar(
    range_sales,
    x="Annual Sales Units",
    y="Range Category",
    orientation="h",
    title="Sales by Vehicle Range Category",
    color="Annual Sales Units",
    color_continuous_scale="Blues"
)

# -----------------------------------
# 10. Dashboard layout
# -----------------------------------

col1, col2, col3 = st.columns([1.2, 1, 1])

with col1:
    st.plotly_chart(fig1, use_container_width=True)

with col2:
    st.plotly_chart(fig2, use_container_width=True)

with col3:
    st.plotly_chart(fig3, use_container_width=True)

col4, col5, col6 = st.columns([1.2, 1, 1])

with col4:
    st.plotly_chart(fig4, use_container_width=True)

with col5:
    st.plotly_chart(fig5, use_container_width=True)

with col6:
    st.plotly_chart(fig6, use_container_width=True)

# -----------------------------------
# 11. Manufacturing Map
# -----------------------------------

st.subheader("Manufacturing Map")

fig7 = px.scatter_geo(
    filtered_df,
    lat="Latitude",
    lon="Longitude",
    color="Manufacturer",
    size="Annual Sales Units",
    hover_name="Manufacturer",
    hover_data=[
        "Market Region",
        "Annual Sales Units",
        "Battery Type"
    ],
    projection="natural earth",
    title="EV Manufacturer Locations"
)

st.plotly_chart(fig7, use_container_width=True)

# -----------------------------------
# 12. Key Market Insights
# -----------------------------------

st.subheader("Key Market Insights")

top_maker = manufacturer_sales.loc[
    manufacturer_sales["Annual Sales Units"].idxmax(),
    "Manufacturer"
]

top_region = regional_sales.loc[
    regional_sales["Annual Sales Units"].idxmax(),
    "Market Region"
]

top_battery = battery_sales.loc[
    battery_sales["Annual Sales Units"].idxmax(),
    "Battery Type"
]

st.write(
    f"**Top manufacturer in sample data:** {top_maker}"
)
st.write(
    f"**Leading region in sample data:** {top_region}"
)
st.write(
    f"**Leading battery type in sample data:** {top_battery}"
)

st.subheader("View Dataset")
st.dataframe(filtered_df, use_container_width=True)

csv = filtered_df.to_csv(index=False).encode("utf-8")

st.download_button(
    label="Download EV Dataset as CSV",
    data=csv,
    file_name="ev_market_data.csv",
    mime="text/csv"
)

st.caption(
    "Educational demonstration only. Sales, ratings, ranges, "
    "and map coordinates are illustrative sample data, "
    "not verified market statistics or factory locations."
)


URL link -- https://public.tableau.com/app/profile/yashika.paliwal/viz/EV_Market_Performance_Market_Story_2026-10-25_17931304697500/EVExecutiveDashboard?publish=yes
