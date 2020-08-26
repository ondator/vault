# Kimball vs Inmon DWH

| \   |      Inmon      |  Kimball |
|----------|:-------------:|------:|
| **approach** |  top-down  | bottom-up |
| **philosophy** |  all entreprise data should be available through big centralized enterprise data warehouse   | in data warehouse should be consolidated only data needed for descision making |
| **design** |    ER model in enterprise data warehouse and dimension model for data marts   |   dimension model |
| **data access** | only through data marts |    direct data warehouse access |