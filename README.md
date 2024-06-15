import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# ჩატვირთეთ მონაცემები
url = 'https://raw.githubusercontent.com/path/to/earthquake-database.csv'
earthquakes = pd.read_csv(url)

# Task 1: მონაცემების დასუფთავება
# 'Date' სვეტის დაყოფა 'Year', 'Month', 'Day' სვეტებად
earthquakes[['Year', 'Month', 'Day']] = earthquakes['Date'].str.split('-', expand=True)

# წაშალეთ რიგები, სადაც ნებისმიერი სვეტი შეიცავს დაკარგულ მნიშვნელობას
earthquakes.dropna(inplace=True)

# Task 2: რიცხვითი ველების კატეგორიზაცია
# 'Magnitude' სვეტის კატეგორიზაცია 'Low', 'Medium', 'High' კატეგორიებად
bins = [0, 5, 7, np.inf]
labels = ['Low', 'Medium', 'High']
earthquakes['Magnitude Category'] = pd.cut(earthquakes['Magnitude'], bins=bins, labels=labels)

# Task 3: მონაცემების ფორმის ცვლილება
# გამოიყენეთ pivot_table მონაცემების შეჯამებისთვის
pivot_table = earthquakes.pivot_table(values='Magnitude', index='Year', columns='Magnitude Category', aggfunc='count')

# გამოიყენეთ melt ფუნქცია პივოტ ცხრილის გასახსნელად
melted = pivot_table.reset_index().melt(id_vars='Year', value_name='Count')

# Task 4: ვიზუალიზაცია
# ხაზოვანი დიაგრამა
plt.figure(figsize=(10, 5))
plt.plot(earthquakes['Date'], earthquakes['Magnitude'])
plt.title('Magnitude over Time')
plt.xlabel('Date')
plt.ylabel('Magnitude')
plt.show()

# ბარის დიაგრამა
earthquakes['Magnitude Category'].value_counts().plot(kind='bar')
plt.title('Magnitude Categories')
plt.xlabel('Category')
plt.ylabel('Count')
plt.show()

# წერტილოვანი დიაგრამა
plt.scatter(earthquakes['Longitude'], earthquakes['Latitude'], c=earthquakes['Magnitude'], cmap='viridis')
plt.title('Earthquake Locations')
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.colorbar(label='Magnitude')
plt.show()

# ჰისტოგრამა
earthquakes['Depth'].hist(bins=30)
plt.title('Histogram of Earthquake Depths')
plt.xlabel('Depth')
plt.ylabel('Frequency')
plt.show()

# Task 5: თარიღის ტრანსფორმაციები
# 'Date' სვეტის კონვერტაცია datetime ფორმატში
earthquakes['Date'] = pd.to_datetime(earthquakes['Date'])

# რეზამპლირება წელიწადში მომხდარი მიწისძვრების რაოდენობის გამოსათვლელად
earthquakes.set_index('Date', inplace=True)
annual_earthquakes = earthquakes.resample('A').size()

# წელიწადში მომხდარი მიწისძვრების ჩვენება
annual_earthquakes.plot()
plt.title('Annual Earthquakes')
plt.xlabel('Year')
plt.ylabel('Count')
plt.show()

