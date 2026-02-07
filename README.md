# retail_store--streamlit-with-mysql
![streamlit1](https://github.com/user-attachments/assets/7a13a3f0-2a79-4dfb-8874-3107d93ffa70)
![streamlit2](https://github.com/user-attachments/assets/f4e2e000-4ce0-44ba-84ea-9bc2c7a0f8d9)
![streamlit3](https://github.com/user-attachments/assets/67a46322-96a8-4aa7-b075-325d1754a874)
![streamlit4](https://github.com/user-attachments/assets/c266381a-c03a-4c93-8f38-0aa12230c2a4)


import streamlit as st

import mysql.connector
import pandas as pd
from tabulate import tabulate
from streamlit_option_menu import option_menu


con = mysql.connector.connect(
    host ="localhost",
    user ="root",
    password ="mani12",
    database="retail_store"
)

osm = con.cursor()
# def get_connection(): ccv 
    # return mysql.connector.connect(**DB_CONFIG**)
st.image("mn.jpg", caption="My Image", width=800)
st.sidebar.image("sample4.jpg", caption="", width=300)
with st.sidebar:
    selected = option_menu("Main Menu", ["Home Page", "product", "sales", "supplier"], icons=["house", "box", "cart", "truck"], menu_icon="cast", default_index=0)


if selected == "Home Page":
    st.markdown("<div style='text-align: center;'><h3>Welcome to E-Commerce Online Shopping</h3></div>", unsafe_allow_html=True)
    st.video("sam2.mp4")
    st.image("online-shopping-ecommerce-website-design-development-500x500.webp", caption="E Commerce World", width=800)


# ---------------- product ----------------
if selected == "product":
    st.header("Product")
    product_id, name,category=st.columns(3)
    product_id = st.number_input("Product ID",min_value=1,key="product_id_input")
    name = st.text_input("Name",key="product_name_input")
    category = st.text_input("Category",key="product_category_input")

    supplier_id,opening_stock ,stock_received=st.columns(3)
    supplier_id = st.text_input("Supplier ID",key="product_supplier_id_input")
    opening_stock = st.number_input("Opening Stock",min_value=0,key="product_opening_stock_input")
    stock_received = st.number_input("Stock Received",min_value=0,key="product_stock_received_input" )
    
    stock_sold,closing_stock=st.columns(2)
    stock_sold = st.number_input("Stock Sold",min_value=0,key="product_stock_sold_input")  
    closing_stock = st.number_input("Closing Stock", min_value=0,key="product_closing_stock_input")
    
    reorder_level,unit_price=st.columns(2)
    reorder_level = st.number_input("Reorder Level",min_value=0,key="product_reorder_level_input")
    unit_price = st.number_input("Unit Price",min_value=0.0,key="product_unit_price_input")
    
    if st.button("Save Product", key="save_product_btn"):
        conn = get_connection()
        cur = conn.cursor()
        cur.execute(
            "INSERT INTO product (product_id, product_name, category, supplier_id, opening_stock, stock_received, stock_sold, closing_stock, reorder_level, unit_price) VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s)",
            (product_id, name, category, supplier_id, opening_stock, stock_received, stock_sold, closing_stock, reorder_level, unit_price)
        )
        conn.commit()
        cur.close()
        conn.close()
        st.success("Product added successfully")
    st.image("sample1.jpg", caption="Product Image", width=800)

# ---------------- sales ----------------
elif selected == "sales":
    st.header("Sales")

    sale_id = st.number_input("Sale ID",min_value=1,key="sale_id_input" )

    product_id = st.text_input("Product ID",key="sale_product_id_input" )

    sale_date = st.date_input("Sale Date",key="sale_date_input")

    quantity_sold = st.number_input("Quantity Sold",min_value=1,key="sale_quantity_sold_input")
    unit_price = st.number_input("Unit Price", min_value=0.0,key="sale_unit_price_input")
    total_sales = quantity_sold * unit_price 
    st.number_input("Total Amount",min_value=0.0,value=total_sales,key="sale_total_amount_input")
    if st.button("Place Sale", key="place_sale_btn"):
        conn = get_connection()
        cur = conn.cursor(dictionary=True)

        # get product info
        cur.execute(
            "SELECT product_name, price FROM product WHERE product_id = %s",(product_id,))
        product = cur.fetchone()

        if product:
            total_amount = quantity * product["price"]

            cur.execute(
                """
                INSERT INTO sales
                (sale_id, customer_id, product_id, product_name,
                 quantity_sold, unit_price, total_sales)
                VALUES (%s,%s,%s,%s,%s,%s,%s)
                """,
                (sale_id,customer_id,product_id,product["product_name"],quantity_sold,unit_price,total_sales))
        

            conn.commit()
            st.success(f"✅ Sale placed! Total = ₹{total_sales}")

        else:
            st.error("❌ Product not found")

        cur.close()
        conn.close()
        st.success("Sale recorded successfully")
    st.image("MN3.jpg", caption="Sales Image", width=700)


# ---------------- supplier ----------------
elif selected == "supplier":
    st.header("Supplier")
    supplier_id, supplier_name = st.columns(2)
    supplier_id  = st.number_input("Supplier ID",min_value=1,key="supplier_id_input")
    supplier_name = st.text_input("Supplier Name",key="supplier_name_input")

    category, contact_number, city = st.columns(3)
    category = st.number_input("Category",min_value=1,key="supplier_category_input")
    contact_number = st.number_input("Contact Number",min_value=0,key="supplier_contact_number_input" )
    city = st.number_input("City",min_value=1,key="supplier_city_input" )

    if st.button("Save Supplier", key="save_supplier_btn"):
        conn = get_connection()
        cur = conn.cursor()
        cur.execute(
            "INSERT INTO supplier (supplier_id, supplier_name, category, contact_number, city) VALUES (%s,%s,%s,%s,%s)",
            (supplier_id, supplier_name, category, contact_number, city)
        )
        conn.commit()
        cur.close()
        conn.close()
        st.success("Supplier added successfully")
    st.image("sam3.png", caption="Supplier Image", width=800)

 

st.sidebar.video("vid2.mp4")
 

