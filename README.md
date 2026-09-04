# dau-sql-project
Расчёт DAU (Daily Active Users) для образовательной платформы
# Проект: Расчёт DAU (Daily Active Users) для образовательной платформы

## Описание задачи(TASK DESCRIPTION)
Рассчитать DAU за первый квартал 2025 года, чтобы определить динамику активности пользователей и эффективность маркетинговых коммуникаций.

##  Стек
- **SQL** (SQLite)
- **Оконные функции** (MAX OVER)
- **Рекурсивные запросы** (WITH RECURSIVE)
- **Визуализация** (Python, Matplotlib)

```sql
WITH RECURSIVE calendar AS (
    SELECT DATE('2025-01-01') AS date_from_calendar
    UNION ALL
    SELECT DATE(date_from_calendar, '+1 day')
    FROM calendar
    WHERE date_from_calendar < DATE('2025-03-31')
),
daily_active_users AS (
    SELECT
        DATE(u.entry_at) AS ymd_from_entry_at,
        COUNT(DISTINCT u.user_id) AS dau
    FROM userentry u
    WHERE DATE(u.entry_at) BETWEEN '2025-01-01' AND '2025-03-31'
    GROUP BY DATE(u.entry_at)
)
SELECT
    c.date_from_calendar,
    COALESCE(d.dau, 0) AS daily_active_users_cnt,
    MAX(COALESCE(d.dau, 0)) OVER (ORDER BY c.date_from_calendar) AS max_dau_cnt,
    COALESCE(d.dau, 0) - MAX(COALESCE(d.dau, 0)) OVER (ORDER BY c.date_from_calendar) AS diff_dau
FROM calendar c
LEFT JOIN daily_active_users d ON c.date_from_calendar = d.ymd_from_entry_at
ORDER BY c.date_from_calendar;
За январь-март 2025 года DAU плавно растёт.

Максимальное значение достигнуто в середине февраля (28 пользователей).

Падение в конце января и в начале марта требует дополнительного исследования (вероятно, технические работы или снижение маркетинговой активности).

С помощью оконных функций (MAX OVER) я отследил динамику отставания от рекордного показателя.
