---
title: Stabilitet ved store krengevinkler
description: Bruk av et KN-kurver til å etablere GZ-kurve for et gitt deplasement og KG. 
Analyse av GZ-kurve mhp DNV stabilitetskriterier.
authors:
  - name: Lars Erik Nygård
    email: lars.e.nygard@ntnu.no
github: https://skipsing.github.io/skipsdesign1/
date: 29.04.23
---



```python
import math
import pandas
import numpy as np 
import matplotlib.pyplot as plt
```

Vi skal finne KN-verdiene for fartøyets krengevinkler ved $\nabla = 10000[tonn]$.Med visuell avlesning av kurvene så finner vi _omtrentlige_ verdier. Avlesningene samler vi i en  [**pandas DataFrame**](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html) der hvert avlesning har formatet $ \text{( krengevinkel , avlest KN )} $ 



```python
stab_data = pandas.DataFrame( [(0,0) , 
                               (10,1.17) , 
                               (20,2.4) , 
                               (30,3.5) , 
                               (40,4.5) , 
                               (50,5.1) , 
                               (60,5.5) , 
                               (70,5.7) ], 
                               columns= ['vinkel', 'kn'] )

stab_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vinkel</th>
      <th>kn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10</td>
      <td>1.17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20</td>
      <td>2.40</td>
    </tr>
    <tr>
      <th>3</th>
      <td>30</td>
      <td>3.50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>40</td>
      <td>4.50</td>
    </tr>
    <tr>
      <th>5</th>
      <td>50</td>
      <td>5.10</td>
    </tr>
    <tr>
      <th>6</th>
      <td>60</td>
      <td>5.50</td>
    </tr>
    <tr>
      <th>7</th>
      <td>70</td>
      <td>5.70</td>
    </tr>
  </tbody>
</table>
</div>



I oppgaven er vi ute etter GZ-verdien. Den finnes ved å benytte den geometriske relasjonen mellom GZ gitt av: 
$$ \text{GZ = KN - KG sin(} \phi\text{)} $$

![image.png](attachment:image.png)

Vi etablerer en ny _kolonne_ med navn **gz** og beregner verdiene ut fra formelen over.   


```python
kg = 6.25 #[m] Gitt i oppgaven
stab_data['gz'] = stab_data['kn'] - kg *  np.sin(np.radians(stab_data['vinkel'])) 
stab_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vinkel</th>
      <th>kn</th>
      <th>gz</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10</td>
      <td>1.17</td>
      <td>0.084699</td>
    </tr>
    <tr>
      <th>2</th>
      <td>20</td>
      <td>2.40</td>
      <td>0.262374</td>
    </tr>
    <tr>
      <th>3</th>
      <td>30</td>
      <td>3.50</td>
      <td>0.375000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>40</td>
      <td>4.50</td>
      <td>0.482577</td>
    </tr>
    <tr>
      <th>5</th>
      <td>50</td>
      <td>5.10</td>
      <td>0.312222</td>
    </tr>
    <tr>
      <th>6</th>
      <td>60</td>
      <td>5.50</td>
      <td>0.087341</td>
    </tr>
    <tr>
      <th>7</th>
      <td>70</td>
      <td>5.70</td>
      <td>-0.173079</td>
    </tr>
  </tbody>
</table>
</div>



Vi sier oss nå fornøyd med datamaterialet og går over på å behandle og presentere materialet vi har samlet. Matplotlib gjør presentasjonsjobben utmerket direkte ut av datarammen: 


```python
stab_data.plot( 'vinkel' , 'gz' )
plt.show()
```


    
![png](stabilitet_store_krengevinkler_files/stabilitet_store_krengevinkler_7_0.png)
    


Plottet viser GZ-verdien som funksjon av krengevinkel. Vi gjør plottet litt mere presentabelt ved å beskrive hva vi faktisk ser, og gir en god beskrivelse av hva de ulike aksene representerer: 


```python
stab_data.plot( 'vinkel' , 'gz' )
plt.xlabel('Krengevinkel [deg]', loc= 'center')
plt.ylabel('GZ-verdi [m]')
plt.title('GZ-kurve @ KG=' + str(kg) +'[m], Depl = 10000[tonn]')
plt.axhline(y=0, color='black', linestyle='-')
plt.show()
```


    
![png](stabilitet_store_krengevinkler_files/stabilitet_store_krengevinkler_9_0.png)
    


Grafisk så kan man se at maksimal GZ verdi oppstår ved krengevinkel 40 grader. 
Vi kan få denne informasjonen ut av direkte ut av datarammen:  


```python
stab_data['gz'].max()
```




    0.48257743945912956



**(Avansert)** Tilhørende vinkel og KN-verdi finnes ved å sette radverdien til *GZ* lik maksimalverdien


```python
max_gz = stab_data[stab_data['gz']==stab_data['gz'].max()]
max_gz
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vinkel</th>
      <th>kn</th>
      <th>gz</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>40</td>
      <td>4.5</td>
      <td>0.482577</td>
    </tr>
  </tbody>
</table>
</div>



## Metasenterets endring ved krenging
Vi skal nå finne hvor mye _metasenteret_ endrer seg under krenging. Vi har at initialavstanden mellom $K$ og $M_0$ er gitt ved 
$$ KM_0 = KG + GM_0$$


```python
gm_0 = 0.305 #[m], gitt som opplysning i oppgaven 
km_0 = kg + gm_0
print('km_0 = ' + str(km_0) + '[m]')
```

    km_0 = 6.555[m]
    

Videre kan vi finne ny KM verdi ved krenging 40grader ut fra den geometriske relasjonen $KM_{40} = KN_{40} \times \sin(40)$, og der vi allerede har fått $KN_{40}$ ut fra avlesning av KN-kurven. 


```python
kn_40 = float(max_gz['kn']) #fra datarammen. Bruker float() for å fjerne formatering  
km_40 = kn_40 / np.sin(np.radians(40))
print('km_40 = ' + str(km_40)  + '[m]')

```

    km_40 = 7.000757220871856[m]
    

Differansen i KM gir oss da endring av metasenteret:  
$$ M_0M_{40} = KM_{40} - KM_{0} $$


```python
m0_m40 = km_40 - km_0
print('Metasenterets endring mellom initialposisjon og 40grader krenging er=' + "%.3f" % (m0_m40) + '[m]' )
```

    Metasenterets endring mellom initialposisjon og 40grader krenging er=0.446[m]
    

## Klasseselskaps krav til areal under GZ-kurve

I oppgaven så er det listet 6 punkter som skal være tilfredstilt for at DNV (klasseselskap) skal kunne godkjenne fartøyet. Vi ser først på de 3 første med krav til areal under GZ-kurven:   
- Areal under GZ-kurven mellom 0-30 grader uttrykt ved $A_{GZ_{0-30}} < 0.055[mrad]$ 
- Areal under GZ-kurven mellom 0-40 grader uttrykt ved $A_{GZ_{0-40}} < 0.09[mrad]$
- Areal under GZ-kurven mellom 30-40 grader uttrykt ved $A_{GZ_{30-40}} < 0.03[mrad]$

Dette kan for enkle kurver som denne regnes manuelt ved å stykke opp arealet i mindre deler bestående av trekanter og rektangler for så å summere de sammen. NB! På x-aksen så er det nå gitt som grader, og det må da regnes om til radianer før en går igang. 

Arealet under en kurve kan bestemmes ved integrasjon $ \int_{a}^{b} f(x) \,dx$, men det fordrer at man vet $f(x)$ som beskriver kurven. I mangel av dette finnes det et par numeriske metoder som løser dette problemet ved kun å vite noen punkter langs kurven:
- [Trapesmetoden](https://no.wikipedia.org/wiki/Trapesintegrasjon)
- [Simpsonmetoden](https://no.wikipedia.org/wiki/Simpson-integrasjon)

Vi går ikke noe nærmere inn på de bortsett fra at vi kan regne på avviket mellom de to. Disse metodene finnes i standardbiblioteket til Python i henholdsvis Scipy og Numpy. 

- Areal under GZ-kurven mellom 0-30 grader uttrykt ved $A_{GZ_{0-30}} < 0.055[mrad]$ 

<img src='./krenging-30-grader.png' width=”400” height='400' >


```python
import scipy
from scipy.integrate import simpson
from numpy import trapz

# Beregner arealet udner kurven ved bruk av trapesmetoden.
area1_30 = trapz( stab_data['gz'][0:4] , dx= np.radians(10))
print("Areal ved trapesmetoden =", "%.5f" % area1_30 , '[mrad]')

# Beregner arealet udner kurven ved bruk av Simpsonmetoden.
area2_30 = simpson(stab_data['gz'][0:4] , dx = np.radians(10))
print("Areal ved Simpsonmetoden =", "%.5f" % area2_30 , '[mrad]')
diff_30 = (area1_30 -area2_30)/ area1_30 * 100 
print('Differansen mellom trapes og Simpson:', "%.2f" % diff_30   , '%')
dnv_30 = 0.055
if min(area1_30, area2_30) >= dnv_30:
    print('Arealet under GZ-kurven mellom 0 og 30 grader er OK ihht DNV kravet') 
else:
    print('Arealet under GZ-kurven mellom 0 og 30 grader er IKKE OK ihht DNV kravet')
```

    Areal ved trapesmetoden = 0.09330 [mrad]
    Areal ved Simpsonmetoden = 0.09289 [mrad]
    Differansen mellom trapes og Simpson: 0.44 %
    Arealet under GZ-kurven mellom 0 og 30 grader er OK ihht DNV kravet
    

- Areal under GZ-kurven mellom 0-40 grader uttrykt ved $A_{GZ_{0-40}} < 0.09[mrad]$

<img src='./krenging-40-grader.png' width=”400” height='400' >


```python

# Beregner arealet under kurven ved bruk av trapesmetoden.
area1_40 = trapz( stab_data['gz'][0:5] , dx= np.radians(10))
print("Areal ved trapesmetoden =", "%.5f" % area1_40 , '[mrad]')

# Beregner arealet udner kurven ved bruk av Simpsonmetoden.
area2_40 = simpson(stab_data['gz'][0:5] , dx = np.radians(10))
print("Areal ved Simpsonmetoden =", "%.5f" % area2_40 , '[mrad]')
diff = (area1_40 -area2_40)/ area1_40 * 100 
print('Differansen mellom trapes og Simpson:', "%.2f" % diff   , '%')
dnv_40 = 0.09
if min(area1_40, area2_40) >= dnv_40:
    print('Arealet under GZ-kurven mellom 0 og 40 grader er OK ihht DNV kravet') 
else:
    print('Arealet under GZ-kurven mellom 0 og 40 grader er IKKE OK ihht DNV kravet')
```

    Areal ved trapesmetoden = 0.16814 [mrad]
    Areal ved Simpsonmetoden = 0.16558 [mrad]
    Differansen mellom trapes og Simpson: 1.52 %
    Arealet under GZ-kurven mellom 0 og 40 grader er OK ihht DNV kravet
    

- Areal under GZ-kurven mellom 30-40 grader uttrykt ved $A_{GZ_{30-40}} < 0.03[mrad]$

<img src='./krenging-30-40-grader.png' width=”400” height='400' >


```python

# Beregner arealet under kurven ved bruk av trapesmetoden.
area1_3040 = trapz( stab_data['gz'][3:5] , dx= np.radians(10))
print("Areal ved trapesmetoden =", "%.5f" % area1_3040 , '[mrad]')

# Beregner arealet udner kurven ved bruk av Simpsonmetoden.
area2_3040 = simpson(stab_data['gz'][3:5] , dx = np.radians(10))
print("Areal ved Simpsonmetoden =", "%.5f" % area2_3040 , '[mrad]')
diff = (area1_3040 -area2_3040)/ area1_3040 * 100 
print('Differansen mellom trapes og Simpson:', "%.2f" % diff   , '%')
dnv_3040 = 0.03
if min(area1_3040, area2_3040) >= dnv_3040:
    print('Arealet under GZ-kurven mellom 30 og 40 grader er OK ihht DNV kravet') 
else:
    print('Arealet under GZ-kurven mellom 30 og 40 grader er IKKE OK ihht DNV kravet')
```

    Areal ved trapesmetoden = 0.07484 [mrad]
    Areal ved Simpsonmetoden = 0.07484 [mrad]
    Differansen mellom trapes og Simpson: 0.00 %
    Arealet under GZ-kurven mellom 30 og 40 grader er OK ihht DNV kravet
    

## Krav til rettende arm

Kravet til "rettende arm" (GZ verdi ved 30 grader) 
- Den rettende arm (GZ) $GZ_{30} >= 0.2[m]$

<img src='./30grader.png' width=”400” height='400' >


```python
test= scipy.interpolate.interp1d(0.3,  stab_data.gz, stab_data.vinkel     )
print(test)
print(np.rad2deg(test))



stab_data.gz

```

    70.0
    4010.7045659157625
    




    0    0.000000
    1    0.084699
    2    0.262374
    3    0.375000
    4    0.482577
    5    0.312222
    6    0.087341
    7   -0.173079
    Name: gz, dtype: float64



## Forklarende plot brukt i løsningsforslaget


```python
# Skisse for area under kurve 30 grader 

stab_data.plot( 'vinkel' , 'gz' )
plt.xlabel('Krengevinkel [deg]', loc= 'center')
plt.ylabel('GZ-verdi [m]')
plt.title('Areal under kurven ved krenging opp til 30grader')
plt.axhline(y=0, color='black', linestyle='-')
plt.fill_between(
        x= stab_data['vinkel']    , 
        y1= stab_data['gz'], 
        where= [True , True , True , True , False , False , False, False ] ,
        color= "b",
        alpha= 0.2)
plt.savefig('krenging-30-grader.png')
plt.close()
```


```python
stab_data.plot( 'vinkel' , 'gz' )
plt.xlabel('Krengevinkel [deg]', loc= 'center')
plt.ylabel('GZ-verdi [m]')
plt.title('Rettende arm > 0.2[m] ved 30[deg] ')
plt.axhline(y=0, color='black', linestyle='-')
plt.axhline(y=0.2, color='red', linestyle='-')
plt.savefig('30grader.png')
plt.close()
```


```python
stab_data.plot( 'vinkel' , 'gz' )
plt.xlabel('Krengevinkel [deg]', loc= 'center')
plt.ylabel('GZ-verdi [m]')
plt.title('Areal under kurven ved kregning opp til 40grader')
plt.axhline(y=0, color='black', linestyle='-')
plt.fill_between(
        x= stab_data['vinkel']    , 
        y1= stab_data['gz'], 
        where= [True , True , True , True , True , False , False, False ] ,
        color= "r",
        alpha= 0.2)
plt.savefig('krenging-40-grader.png')
plt.close()
```


```python
stab_data.plot( 'vinkel' , 'gz' )
plt.xlabel('Krengevinkel [deg]', loc= 'center')
plt.ylabel('GZ-verdi [m]')
plt.title('Areal under kurven ved krenging mellom 30-40grader')
plt.axhline(y=0, color='black', linestyle='-')
plt.fill_between(
        x= stab_data['vinkel']    , 
        y1= stab_data['gz'], 
        where= [False , False , False , True , True , False , False, False ] ,
        color= "r",
        alpha= 0.2)
plt.savefig('krenging-30-40-grader.png')
plt.close()
```


```python
!dir
```

     Volume in drive C is OSDisk
     Volume Serial Number is FCEF-E3D7
    
     Directory of c:\Users\larsenyg\repos\skipsdesign1\exercises
    
    30.04.2023  13:57    <DIR>          .
    30.04.2023  13:57    <DIR>          ..
    30.04.2023  13:58            31�767 30grader.png
    30.04.2023  13:35            33�070 krenging-30-40-grader.png
    30.04.2023  13:58            32�559 krenging-30-grader.png
    30.04.2023  13:35            32�477 krenging-40-grader.png
    30.04.2023  13:56            31�767 rettende-arm-30grader.png
    30.04.2023  13:57            31�767 rettende-arm.png
    29.04.2023  10:27            41�672 stabilitet_store_krengevinkler.ipynb
    29.04.2023  08:46               161 test.md
                   8 File(s)        235�240 bytes
                   2 Dir(s)  404�630�732�800 bytes free
    


```python

```
