# Troubleshooting-OSPF-lab
Во время прохождения курса CCNA 200-301 от Jeremy IT Lab, я решал лабораторные работы. Из всех работ, она выделилась тем, что здесь нужно было решать проблемы. У некоторых роутеров не устанавливалось соседство и хосты не могли выходить в Интернет. Ниже будет описание того, как я решал проблемы и какие у меня были предположения.

Сама лабораторная работа:

<img width="1366" height="716" alt="Снимок экрана в 2026-02-11 19-31-21" src="https://github.com/user-attachments/assets/08c81a35-78bc-4713-8bdf-ed049eaf79a3" />

ТЗ:

The network has been pre-configured (IP addresses, OSPF)

1. The connection between R1 and R2 has been newly added.
  -Configure the serial connection between R1 and R2 (clock rate of 128000).
  -Configure OSPF on R1 and R2.✅

2. Only R3 has a route to 10.0.2.0/24.  Why?  Fix the problem. 
R3 been OSPF network type P-P✅

3. R2 and R4 won't become OSPF neighbors with R5.  Why?  Fix the problem. 
R5 hello - dead interval mismatch with R2 R4 settings✅

4. PC1 and PC2 can't ping the external server 8.8.8.8.  Why? Fix the problem. 
R5 not have default router in table rouer✅

5. Examine the LSDB.  What LSAs are present?

1. Я настроил последовательный интерфейс на обоих маршрутизаторах. Затем настроил OSPF маршрутизацию. Соседство было установлено.

2. R3 не изучал маршруты от соседа R4 по одной причине: тип OSPF сети различался. у R3 тип сети "Точка-Точка", в то же время, как у R4 широковещательный. 

```
no ip ospf network point-to-point
```

Эта команда решила проблему и R3 наконец изучил маршруты соседа R4.

3. Было несколько вариантов. Начиная от различий в MTU, заканчивая интервалами hello сообщений и dead.
В процессе проверки командой, выяснилось что проблема была в различии Hello и Dead интервалов. 5 и 30 секунд, вместо 10 и 40 секунд по умолчанию.

```
>en
#show ip ospf interface g0/0 ! интерфейс, который должен был участвовать в соседстве с R4 и R2
```

4. У маршрутизатора R5 не было шлюза по умолчанию. После добавления и анонсирования командой, шлюз по умолчанию добавился у всех роутеров и хосты могли выходить в интернет. А также этот маршрутизатор становится ASBR.

```
default-information originate
```

5. 

```
> en
# show ip ospf database
```

<img width="1366" height="716" alt="изображение" src="https://github.com/user-attachments/assets/21eebe68-6fa1-4c64-badd-f383fa762b74" />


Показаны LSA type 1, 2 и 5.

LSA type 1 это объявление о состоянии линков, которые объявляют роутеры, LSA type 2 объявления о сетях, которые есть у маршрутизаторов, LSA 5 объявление, которое генерирует маршрутизатор ASBR.
