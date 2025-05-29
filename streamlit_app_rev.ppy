import streamlit as st
from snowflake.snowpark.context import get_active_session
from snowflake.snowpark.functions import col

st.title(":cup_with_straw: Customize Your Smoothie!:cup_with_straw:")
st.write(
  """Choose the fruits you want in your custome Smoothie!
  """
)

name_on_order = st.text_input("Name on Smoothie:")
st.write("The name on your Smoothie will be:", name_on_order)

# Use st.secrets to connect to Snowflake
try:
    session = st.session_state.session
except AttributeError:
    session = None

if session is None:
    try:
        session = get_active_session()
    except:
        try:
            cnx = st.connection("snowflake")
            session = cnx.session()
            st.session_state.session = session
        except Exception as e:
            st.error(f"Error connecting to Snowflake: {e}")
            st.stop()

my_dataframe = session.table("smoothies.public.fruit_options").select(col('FRUIT_NAME'))

ingredients_list = st.multiselect(
    'Choose up to 5 ingredients:'
    , my_dataframe.collect() # Convert to a Pandas DataFrame for multiselect
    , max_selections=5
)

if ingredients_list:
    ingredients_string = ' '.join(ingredients_list)

    my_insert_stmt = f"""
        insert into smoothies.public.orders(ingredients, name_on_order)
        values ('{ingredients_string}', '{name_on_order}')
    """

    time_to_insert = st.button('Submit Order')

    if time_to_insert:
        try:
            session.sql(my_insert_stmt).collect()
            st.success(f'Your Smoothie is ordered, {name_on_order}!', icon="✅")
        except Exception as e:
            st.error(f"Error inserting data: {e}")
