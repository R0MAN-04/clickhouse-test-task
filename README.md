# clickhouse-test-task
Тестове завдання на основі датасету:  https://www.kaggle.com/datasets/oles04/bundesliga-soccer-player

Переважний діалект SQL для вирішення задачі - ClickHouse

1) Вивести топ-3 клуби із найдорожчим захистом (Defender-*)
2) У розрізі клубу та гравця порахувати, скільки гравців підписало контракт із клубом після нього
3) Вибрати клуби, де середня вартість французьких гравців більша за 5 млн
4) Вибрати клуби, де частка німців вища за 90%
5) Вибрати найдорожчого у своєму віці (на виході ім'я + вартість)
6) Вибрати гравців, з вартістю у 1.5 рази більше, ніж у середньому за своєю позицією
7) На якій позиції найважче отримати контракт з будь-якою компанією
8) Порахувати, в якій команді найперше закінчиться контракт у 5 гравців
9) У якому віці гравці здебільшого виходять на пік своєї вартості
10) У якої команди найзіграніший склад (найдовше грають разом)
11) У яких командах є тезки
12) Вивести команди, де топ-3 гравці займають 50% платіжної відомості


# Bundesliga Players – ClickHouse Test Task

## Джерело даних
Дані взято з Kaggle:  
[Bundesliga Soccer Player Dataset](https://www.kaggle.com/datasets/oles04/bundesliga-soccer-player)

---

##  Завдання та рішення

### 1) Топ-3 клуби з найдорожчим захистом
```sql
SELECT 
    club,
    SUM(price) AS total_defence_value
FROM bundesliga_player
WHERE position LIKE 'Defender -%'
GROUP BY club
ORDER BY total_defence_value DESC
LIMIT 3;

### 2) У розрізі клубу та гравця порахувати, скільки гравців підписало контракт із клубом після нього
```sql
SELECT
    bp1.club,
    bp1.name,
    COUNT(*) AS players_after
FROM bundesliga_player AS bp1
JOIN bundesliga_player AS bp2
  ON bp1.club = bp2.club
 AND bp2.joined_club > bp1.joined_club
GROUP BY bp1.club, bp1.name
ORDER BY bp1.club, players_after DESC;
