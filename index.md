## Ejercicio 3 
Equipo 3 : Noria Montes Itzel y Vega Alba Mónica Kendra.


Primero instalamos las siguientes paqueterias: 

```markdown
install.packages("tseries")
install.packages("astsa")
install.packages("forecast")
install.packages("nortest")
install.packages("stats")
install.packages("datasets")

library(tseries)
library(astsa)
library(forecast)
library(nortest)
library(stats)
library(datasets)
library(TSA)
```
Lectura de datos, lee tu base de datos y asignala a una variable x

```markdown
data(wages)
x= wages
```
¿Cuál es la frecuencia de tus datos (número de observaciones por año)?
```markdown
Frecuencia= frequency(x)
print(Frecuencia)
```
```markdown
[1] 12
```
Realiza la gráfica a través de una línea y observa sus resultados
```markdown
plot(x, lty = c(1,2),main ="Salarios promedio por hora en la industria del vestido",cex.main=1.5, col= "darkturquoise")
```
![g1](https://user-images.githubusercontent.com/57273828/69299222-a7d64b00-0bd5-11ea-94e7-78f22750a367.png)

a) ¿Cuál es la media de un proceso Autorregresivo?

```markdown
MediaAutorregresivo= 0
```
b) ¿Cuál es la media de una Media Móvil?
```markdown
MediaMediaMovil= 0
```
De la anterior gráfica ¿Se puede ajustar un Modelo ARMA con los datos como se tienen en este momento?
```markdown
ARMATRUEFALSE=  FALSE #TRUE (Sí), FALSE (NO)
```
Disminuya un poco la variabilidad cálculando la transformación Box-Cox
```markdown
BoxCox.lambda(x, method="loglik", lower=0)
```
```markdown
[1] 2
```
 ¿Cúal es valor de n en la expresión Y^n, tal que disminuye la variabilidad?
```markdown
ExponenteLambda= 2
```
A partir de este momento trabajaremos con la transformación log(Datos^lambda), para ello escribe la transformación.
```markdown
par(mfrow=c(1,2)) 
TransformacionDatos= log(x^2)
```
```markdown
plot(x, main="Modelo Original", xlab="Años", ylab="Salario Promedio", col="darkcyan")
plot(TransformacionDatos, main="Modelo con Transformación", xlab="Años", ylab="Salario Promedio", col="darkcyan")
```
![g2](https://user-images.githubusercontent.com/57273828/69299257-be7ca200-0bd5-11ea-8c6d-fdfe51eaee33.png)

Con la nueva transformación de datos realiza la operación de diferenciación (operación dórito), con el año siguiente, ie, restar enero 2019 - enero 2020 o t1 2010-t1 2011. Recuerde usar sólo la función diff( ,lag=).
```markdown
Dorito= diff((TransformacionDatos),lag = 4) 
```
Con la nueva serie generada realiza su gráfica lineal.
```markdown
par(mfrow=c(1,1))
plot(Dorito, main="Modelo estacionario", xlab="Años", ylab="Diferencia de salarios respecto al año  inmediato siguiente")
abline(h=mean(Dorito),col = "darkcyan", lwd=3, lty=2)
```
![g3](https://user-images.githubusercontent.com/57273828/69299288-d3f1cc00-0bd5-11ea-93ec-30dcae0a09cf.png)

De la anterior gráfica ¿Se puede ajustar un Modelo ARMA con los datos como se tienen en este momento?
```markdown
ARMADorito= TRUE #TRUE (Sí), FALSE (NO)
```
La serie de datos es estacionaria con media 0
```markdown
Estacionario= FALSE #TRUE (Sí), FALSE (NO)
```
Realiza la prueba  Phillips-Perron y verifica que la serie sea estacionaria
```markdown
PP= pp.test(Dorito)
PP
```
```markdown
Phillips-Perron Unit Root Test

data:  Dorito
Dickey-Fuller Z(alpha) = -27.992, Truncation lag parameter = 3, p-value =
0.01
alternative hypothesis: stationary
```
La serie es estacionaria a través de la anterior prueba
```markdown
EstacionarioPP= TRUE

#"Se rechaza H0, por lo que es estacionaria con un 99% de conf"
```
Realiza el correlegrama de tu datos
```markdown
Correlograma= acf(Dorito, main="Autocorrelaciones del modelo")
```
![g4](https://user-images.githubusercontent.com/57273828/69299323-e966f600-0bd5-11ea-8ad6-2e1e000cdff9.png)

¿Tus datos estan autocorrelacionados entre sí?
```markdown
Autocorrelacion= TRUE #TRUE (Sí), FALSE (NO)
```
¿Cúantas líneas se salen de la banda de confianza? (Considerando la primera linea)
```markdown
AutocorrelacionNum= 8
```
Realiza el correlegrama de autocorrelaciones parciales de tu datos
```markdown
CorrelogramaParcial= pacf(Dorito, main="Autocorrelaciones parciales del modelo")
```
![g5](https://user-images.githubusercontent.com/57273828/69299357-ff74b680-0bd5-11ea-84c2-92d15cf1660b.png)

¿Cúantas líneas se salen de la banda de confianza? (Considerando la primera linea)
```markdown
AutocorrelacionNum2= 5
```
De los anteriores resultados cual podría ser una primera propuesta para AR(P)
```markdown
Pestimado= 8
```
De los anteriores resultados cual podría ser una primera propuesta para MA(q)
```markdown
qestimado= 5
```
Por lo que el modelo ARMA(p,q) debe ser:
```markdown
ARMApara=c(8,5)
```
Ajusta con estos datos tú modelo ARMA y obten los coeficientes alfa y theta
```markdown
ModeloARMA= arima(Dorito, order=c(8,0,5))
ModeloARMA
```
```markdown
Call:
arima(x = Dorito, order = c(8, 0, 5))

Coefficients:
          ar1      ar2     ar3      ar4      ar5      ar6     ar7      ar8     ma1
      -0.8495  -0.3959  0.4197  -0.0025  -0.0261  -0.1536  0.1852  -0.1598  1.8454
s.e.      NaN      NaN  0.1393   0.0914   0.1913   0.1770  0.1763   0.1657     NaN
         ma2     ma3     ma4     ma5  intercept
      2.4061  2.1826  1.2197  0.3163     0.0229
s.e.     NaN     NaN     NaN     NaN     0.0046

sigma^2 estimated as 7.043e-05:  log likelihood = 222.47,  aic = -416.93
```
Realiza una predicción por 5 años (considera la frequencia)
```markdown
Prediccion= predict(ModeloARMA, n.ahead= 60)
Prediccion$pred
```
```markdown
   Jan         Feb         Mar         Apr         May         Jun
1987                                                                        
1988 0.023777385 0.023108304 0.022167294 0.025871243 0.022878584 0.018957304
1989 0.023238415 0.022462828 0.022082468 0.023992059 0.023612966 0.019713370
1990 0.023368270 0.022561556 0.022505674 0.023207101 0.023862427 0.020624407
1991 0.023371194 0.022622396 0.022767370 0.022836584 0.023782724 0.021407747
1992 0.023315788 0.022673600 0.022905498 0.022702873 0.023584359 0.021997850
             Jul         Aug         Sep         Oct         Nov         Dec
1987 0.016568498 0.003590802 0.019191404 0.026181081 0.016127358 0.027312544
1988 0.029268452 0.018480702 0.022414330 0.027122491 0.018409834 0.024813273
1989 0.026818942 0.020976268 0.021607998 0.026146482 0.020144881 0.023764980
1990 0.025121790 0.022329491 0.021464960 0.025194324 0.021307601 0.023166659
1991 0.024033044 0.022991812 0.021642531 0.024420060 0.022057193 0.022861754
1992                                                                        
```
Realiza la gráfica y ve el valor proyectado.
```markdown
 plot(Dorito,xlim= c(1982,1995), col= "darkturquoise")
  lines(Prediccion$pred, col =" darkviolet", lty= 1)
```
![g6](https://user-images.githubusercontent.com/57273828/69299397-161b0d80-0bd6-11ea-9191-2eb2040bac78.png)

Propón un modelo un menor AIC, si no lo encuentras busca el que mejor AIC te de y ve la última gráfica generada para decidir que parametros se apegan mása tus datos. ¿Cuál es tu modelo propuesto?
```markdown
ARMApara2=c(9,5)
```
Ajusta con estos datos tú modelo ARMA y obten los coeficientes alfa y theta
```markdown
ModeloARMA2= arima(Dorito, order=c(9,0,5))
ModeloARMA2
```
```markdown
Call:
arima(x = Dorito, order = c(9, 0, 5))

Coefficients:
         ar1      ar2     ar3      ar4     ar5      ar6     ar7      ar8     ar9
      0.9559  -0.1582  0.0990  -0.8545  0.8782  -0.0612  0.0198  -0.7770  0.7523
s.e.  0.0680   0.1526  0.1335   0.1531  0.1602   0.1198  0.1332   0.1099  0.1048
          ma1     ma2     ma3     ma4     ma5  intercept
      -0.2123  0.4145  0.4583  0.1403  0.1439     0.0250
s.e.   0.1300  0.1857  0.2122  0.2022  0.2454     0.0096

sigma^2 estimated as 4.959e-05:  log likelihood = 234.11,  aic = -438.21
```
Realiza una predicción por 8 años (considera la frequencia)
```markdown
Prediccion2= predict(ModeloARMA2, n.ahead =96)
Prediccion2$pred
```
```markdown
         Jan          Feb          Mar          Apr          May          Jun
1987                                                                              
1988  0.029103932  0.023709551  0.016075611  0.015494704  0.011045870  0.011353074
1989  0.030278651  0.022519903  0.016741190  0.017946102  0.017255749  0.018508945
1990  0.029846896  0.021632979  0.017350966  0.020043630  0.021555105  0.022861711
1991  0.028804771  0.021069503  0.018036136  0.021800367  0.024493405  0.025366453
1992  0.027597300  0.020756472  0.018809069  0.023253488  0.026454378  0.026667665
1993  0.026416983  0.020622803  0.019647594  0.024448725  0.027708489  0.027194589
1994  0.025344468  0.020621534  0.020523806  0.025428048  0.028446928  0.027230860
1995  0.024412348  0.020726704  0.021411909  0.026224105  0.028805518  0.026963858
              Jul          Aug          Sep          Oct          Nov          Dec
1987  0.008374212 -0.014906871 -0.002945493  0.004291476  0.006800711  0.030640774
1988  0.018138309  0.001590922  0.007509518  0.015207760  0.016598640  0.031996675
1989  0.023179046  0.010795068  0.013108685  0.020613305  0.022024850  0.031725537
1990  0.025438629  0.015773629  0.016186395  0.023295892  0.025124383  0.030946463
1991  0.026119722  0.018370861  0.018012129  0.024697808  0.026949115  0.030083721
1992  0.025955476  0.019677608  0.019252400  0.025531958  0.028035349  0.029263487
1993  0.025384742  0.020327252  0.020240097  0.026127349  0.028657722  0.028500257
1994  0.024664607  0.020679039  0.021128551  0.026618762  0.028961282  0.027778164
1995
```
Realiza la gráfica y ve el valor proyectado.
```markdown
plot(Dorito,xlim= c(1982,1995), col= "darkturquoise")
lines(Prediccion2$pred, col =" darkviolet", lty= 1)
```
![g7](https://user-images.githubusercontent.com/57273828/69299430-27641a00-0bd6-11ea-922a-648ed911d472.png)

¿Tiene sentido la última proyección?, si no lo tiene busca cambiando los parametros como desees
```markdown
Sentido = FALSE
```
Dado que los datos del modelo 2 también tienden a la media, aunque de manera más lenta, proponemos un tercer modelo, en este caso ARMA(9,18)
```markdown
ARMApara3= c(9,18)

ModeloARMA3= arima(Dorito, order=c(9,0,18))
ModeloARMA3
```
```markdown
Call:
arima(x = Dorito, order = c(9, 0, 18))

Coefficients:
         ar1      ar2     ar3      ar4     ar5      ar6     ar7      ar8     ar9
      0.9336  -0.0486  0.0602  -1.0069  0.9654  -0.1228  0.0482  -0.9749  0.8848
s.e.  0.1022   0.0756  0.0726   0.0680  0.1018   0.0796  0.0752   0.0602  0.0968
         ma1     ma2     ma3     ma4     ma5     ma6     ma7     ma8     ma9
      -0.484  0.2411  0.2822  0.2812  0.0557  0.3038  0.2921  0.3494  0.6046
s.e.   0.329  0.2618  0.3168  0.2754  0.1987  0.3214  0.2253  0.2693  0.2585
        ma10    ma11     ma12    ma13     ma14     ma15    ma16    ma17    ma18
      0.3104  0.0861  -0.2169  0.7903  -0.1749  -0.0108  0.4177  0.0549  0.1140
s.e.  0.2405  0.2214   0.2540  0.2890   0.2515   0.3002  0.3928  0.2933  0.3108
      intercept
         0.0237
s.e.     0.0081

sigma^2 estimated as 2.197e-05:  log likelihood = 248.67,  aic = -441.33
```
Predicción a 8 años
```markdown
Prediccion3 = predict(ModeloARMA3, n.ahead =96 )
Prediccion3$pred
```
```markdown
 Jan           Feb           Mar           Apr           May
1987                                                                      
1988  0.0314545923  0.0301796500  0.0227161766  0.0155532758  0.0168463463
1989  0.0376704580  0.0353093999  0.0309310313  0.0230917133  0.0263230931
1990  0.0389056429  0.0356584196  0.0344388406  0.0265259004  0.0315895332
1991  0.0382130764  0.0341055707  0.0362478881  0.0287144830  0.0352072249
1992  0.0368124852  0.0318499215  0.0373184546  0.0305574610  0.0379764905
1993  0.0350925429  0.0293341993  0.0379333567  0.0323221391  0.0401431417
1994  0.0331769764  0.0267555606  0.0381665498  0.0340681441  0.0417848148
1995  0.0311069079  0.0242275035  0.0380338465  0.0357841651  0.0429341431
               Jun           Jul           Aug           Sep           Oct
1987                0.0087847440 -0.0169562040 -0.0055029154  0.0006556228
1988  0.0090458032  0.0141250747 -0.0037104458 -0.0031023453  0.0090463494
1989  0.0165894138  0.0184632807  0.0011946441 -0.0015759692  0.0120355103
1990  0.0208406363  0.0196037109  0.0035032258 -0.0021621483  0.0127485242
1991  0.0241105867  0.0197377070  0.0051429037 -0.0030273528  0.0127887169
1992  0.0270903904  0.0195923490  0.0067006135 -0.0035650759  0.0126604081
1993  0.0299500121  0.0194260141  0.0083324438 -0.0035723815  0.0125203182
1994  0.0326918171  0.0193457192  0.0100566572 -0.0029868288  0.0124183846
1995  0.0352655293                                                        
               Nov           Dec
1987  0.0040271278  0.0340610093
1988  0.0117905760  0.0384922288
1989  0.0139589086  0.0392417112
1990  0.0141559671  0.0376198799
1991  0.0138798877  0.0350263761
1992  0.0136124159  0.0319599229
1993  0.0135020547  0.0286366732
1994  0.0135837116  0.0251862346
1995
```
Ahora procedemos a graficar la predicción con los datos anteriores produciendo la siguiente gráfica. Donde las líneas de color turquesa corresponden a la serie original y la línea violeta corresponde al pronóstico hecho
```markdown
plot(Dorito,xlim= c(1982,1995), col= "darkturquoise")
lines(Prediccion3$pred, col =" darkviolet", lty= 1)
```
![g8](https://user-images.githubusercontent.com/57273828/69299480-3fd43480-0bd6-11ea-945e-25a6a8d1bfc6.png)
