# Лабораторна робота №2
## Data-driven підходи у моделюванні



#### Дані за 24-годинний цикл


```
temps = np.array([75, 77, 76, 73, 69, 68, 63, 59, 57, 55, 54, 52, 50, 50, 49, 49, 49, 50, 54, 56, 59, 63, 67, 72])
times = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24])
```


Цей код створює два масиви: "temps" (температура) і "times" (час). Масиви містять дані про температуру та час для 24-годинного циклу.


#### Інтерполяція
```
x_interp = np.arange(1, 24.01, 0.01)  # Крок 0.01 для більшої точності
f_linear_interp = interpolate.interp1d(times, temps, kind='linear', fill_value='extrapolate')
f_spline_interp = interpolate.interp1d(times, temps, kind='cubic', fill_value='extrapolate')
```


Цей код використовує два методи інтерполяції: лінійну та сплайн, щоб отримати значення температури для будь-якого моменту часу в межах 24-годинного циклу.


#### Косинусне апроксимування


```
def cos_model(x, A, B, C):
    return A * np.cos(B * x) + C

popt, _ = optimize.curve_fit(cos_model, times, temps, p0=[10, 0.1, 50])
A, B, C = popt
temps_cos_pred = cos_model(times, A, B, C)  # Прогноз на основі косинусного апроксимування
```


Цей код використовує косинусну функцію для апроксимації залежності температури від часу. Коефіцієнти A, B, C підбираються за допомогою методу оптимізації curve_fit.


#### Лінійна регресія


```
E2_f_cos = np.sqrt(np.mean((temps_cos_pred - temps) ** 2))
E2_f_linear_interp = np.sqrt(np.mean((f_linear_interp(x_interp) - f_spline_interp(x_interp)) ** 2))
E2_f_spline_interp = np.sqrt(np.mean((f_spline_interp(x_interp) - f_linear_interp
```

Усе це виводить графік:


![alt graphic](https://media.discordapp.net/attachments/917547349864230912/1231690364419575909/image.png?ex=6637e00a&is=66256b0a&hm=d984216540b9b082fa726ab9be7331732ea65f5829e4e6b85c8135dfece8246f&=&format=webp&quality=lossless)


А також консоль виведе


```
Похибка E2(f) для косинусного апроксимування: 1.2514027261973517
Похибка E2(f) для параболічного апроксимування: 2.679857555105914
Похибка E2(f) для лінійної інтерполяції: 0.16086708770364208
Похибка E2(f) для сплайн інтерполяції: 0.16086708770364208
```


### Отримані результати для похибки 


𝐸2(𝑓)  різних методів апроксимації та інтерполяції можуть бути інтерпретовані наступним чином:


 - Косинусне апроксимування: Похибка 𝐸2(𝑓) дорівнює 1.251, що вказує на помірну відповідність між даними і моделлю косинусного апроксимування. Цей метод може підходити для апроксимації температурних даних, які мають коливальний характер.
 - Параболічне апроксимування: Похибка 𝐸2(𝑓) дорівнює 2.680, що вказує на більшу похибку порівняно з іншими методами. Це свідчить про те, що параболічне апроксимування може бути не дуже точним для даного набору даних, особливо якщо дані мають більше ніж одну точку максимума або мінімуму.
 - Лінійна інтерполяція: Похибка 𝐸2(𝑓)дорівнює 0.161, що вказує на високу точність цього методу. Лінійна інтерполяція в даному випадку забезпечує хорошу відповідність між прогнозованими і реальними даними.
 - Сплайн інтерполяція: Похибка 𝐸2(𝑓) також дорівнює 0.161, що свідчить про те, що цей метод забезпечує аналогічну точність, як і лінійна інтерполяція. Сплайн інтерполяція може краще відображати гладкість даних у порівнянні з лінійною інтерполяцією.


Таким чином, лінійна інтерполяція та сплайн інтерполяція є найбільш точними методами для даного набору даних, тоді як косинусне та параболічне апроксимування показують більшу похибку. Якщо потрібна висока точність, варто обирати між лінійною інтерполяцією та сплайн інтерполяцією. Косинусне апроксимування може підійти, якщо дані мають характерні періодичні коливання, проте для цього набору даних цей метод є менш точним. Параболічне апроксимування, хоча й підходить для деяких даних, не найкращий вибір для цього конкретного набору.


### Аналіз поліноміальної регресії з регуляризацією для прогнозування температури


```
temps_original = np.array([75, 77, 76, 73, 69, 68, 63, 59, 57, 55, 54, 52, 50, 50, 49, 49, 49, 50, 54, 56, 59, 63, 67, 72])
hours = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24])
```


Цей код створює два масиви: "temps_original" (температура) і "hours" (час). Вони містять дані про температуру та час для 24-годинного циклу.


```
degree = 10
poly = PolynomialFeatures(degree=degree)
```


Цей код створює об'єкт класу PolynomialFeatures, який буде використовуватися для генерації поліноміальних ознак. Параметр "degree" визначає максимальний ступінь членів у поліномі.


```
# Метод найменших квадратів (Ordinary Least Squares - OLS)
model_ols = make_pipeline(poly, LinearRegression())

# LASSO регресія
model_lasso = make_pipeline(poly, Lasso(alpha=0.1))

# Ridge регресія
model_ridge = make_pipeline(poly, Ridge(alpha=0.1))

# Elastic Net регресія
model_elastic = make_pipeline(poly, ElasticNet(alpha=0.1, l1_ratio=0.5))
```


Цей код створює конвеєри (pipelines) для кожного методу регресії. Конвеєр поєднує поліноміальне перетворення ознак з обраним методом лінійної регресії з регуляризацією. Параметр "alpha" визначає силу регуляризації.


```
# Навчання моделей
model_ols.fit(hours.reshape(-1, 1), temps_original)
model_lasso.fit(hours.reshape(-1, 1), temps_original)
model_ridge.fit(hours.reshape(-1, 1), temps_original)
model_elastic.fit(hours.reshape(-1, 1), temps_original)

# Прогнозування та оцінка помилки
temps_ols_pred = model_ols.predict(hours.reshape(-1, 1))
ols_error = np.sqrt(mean_squared_error(temps_original, temps_ols_pred))

# ... аналогічно для інших моделей
```


Цей код навчає кожну модель на даних про температуру та час. Після навчання робить прогнози температури та обчислює кореневу середню квадратичну похибку (RMSE) для оцінки точності моделі.


До консолі виведеться:
```
Похибка E2(f) до "зламу":
OLS: 0.5251490672123931
LASSO: 1.1571223302326747
Ridge: 0.5769158653066447
Elastic Net: 1.1439264598430863

Похибка E2(f) після "зламу":
OLS: 8.705567925143843
LASSO: 9.603549386855006
Ridge: 8.844459344988739
Elastic Net: 9.596744410753812
```

## Аналіз результатів, отриманих після виконання різних методів регресії та порівняння їхньої стійкості до викидів:

### До "зламу" даних:
#### Похибка 𝐸2(𝑓) для моделей до внесення аномалії:
 - OLS (звичайні найменші квадрати): 0.5251490672123931
 - LASSO: 1.1571223302326747
 - Ridge: 0.5769158653066447
 - Elastic Net: 1.1439264598430863


Найнижчі похибки показали OLS і Ridge, що свідчить про хорошу відповідність до "зламу". LASSO та Elastic Net мають вищі похибки, ймовірно, через сильнішу регуляризацію.


### Після "зламу" даних:
#### Похибка 𝐸2(𝑓) після внесення аномалії:
 - OLS: 8.705567925143843
 - LASSO: 9.603549386855006
 - Ridge: 8.844459344988739
 - Elastic Net: 9.596744410753812


Усі методи демонструють значне збільшення похибки після "зламу". Вони стали набагато менш точними внаслідок наявності аномалії в даних.


Оскільки після внесення аномалії всі моделі демонструють суттєве зростання похибки, можна сказати, що жоден з методів не є достатньо стійким до викидів


Найменші квадрати (OLS) і Ridge показують меншу похибку після "зламу" порівняно з LASSO і Elastic Net, але всі вони значно втрачають точність.


### На графіку це виглядає ось так


![alt графік](https://media.discordapp.net/attachments/917547349864230912/1231694372869902406/image.png?ex=6637e3c6&is=66256ec6&hm=f2f75bce3fd1e4fe7cace50848ce6d8c843297d2e175e979ed759a1e924d777e&=&format=webp&quality=lossless)
