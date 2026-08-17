# Daşınmaz Əmlak Qiymətlərinin Proqnozlaşdırılması

Bu layihədə Azərbaycan daşınmaz əmlak bazarına aid elan məlumatları üzərində maşın öyrənməsi və Exploratory Data Analysis (EDA) yanaşmalarından istifadə edilmişdir. Layihənin əsas məqsədi daşınmaz əmlak qiymətlərinə təsir edən əsas faktorları müəyyən etmək və müxtəlif maşın öyrənməsi modelləri vasitəsilə əmlakların ümumi qiymətini proqnozlaşdırmaqdır.

## Layihənin məqsədi

Layihə çərçivəsində aşağıdakı əsas analizlər həyata keçirilmişdir:

- Daşınmaz əmlak elan məlumatlarının təhlili və hazırlanması
- EDA və Data Cleaning proseslərinin aparılması
- Çatışmayan dəyərlərin və outlier-lərin araşdırılması
- Qiymətə təsir edən əsas xüsusiyyətlərin müəyyənləşdirilməsi
- Numerik və kateqorik dəyişənlərin preprocessing edilməsi
- Train və test məlumatlarının preprocessing-dən əvvəl bölünməsi
- Linear Regression modelinin qurulması
- Random Forest Regressor modelinin qurulması
- Gradient Boosting Regressor modelinin qurulması
- Modellərin MAE, RMSE və R² metrikləri ilə qiymətləndirilməsi
- 5-Fold Cross-Validation vasitəsilə modellərin müqayisə edilməsi
- RandomizedSearchCV vasitəsilə ən yaxşı modelin hyperparameter tuning edilməsi
- Yekun modelin ayrılmış test datasetində qiymətləndirilməsi
- Hazırlanmış modelin `joblib` vasitəsilə saxlanılması

## Dataset

Layihədə **100,775 daşınmaz əmlak elanı** istifadə edilmişdir.

Datasetdə əmlakın yerləşdiyi məkan, şəhər, sahəsi, otaq sayı, mərtəbəsi, bina növü, kateqoriyası, təmir vəziyyəti, çıxarış, ipoteka, sahibin statusu, agentlik məlumatları və coğrafi koordinatlar kimi xüsusiyyətlər mövcuddur.

Target dəyişən olaraq:

**`Ümumi_qiymət`**

seçilmişdir.

## EDA və Data Cleaning

İlkin mərhələdə datasetin strukturu, dəyişənlərin statistik göstəriciləri və məlumat keyfiyyəti araşdırılmışdır.

Data Cleaning mərhələsində:

- Çatışmayan dəyərlər müəyyən edilmiş və analiz edilmişdir.
- Lazımsız və model üçün uyğun olmayan sütunlar çıxarılmışdır.
- Məlumat tipləri uyğunlaşdırılmışdır.
- Kateqorik dəyişənlər `string` tipinə çevrilmişdir.
- Çatışmayan dəyərlərin mövcudluğunu göstərmək üçün `missing` dəyişənləri yaradılmışdır.
- `True/False` tipli missing göstəriciləri `0/1` formatına çevrilmişdir.
- Target ilə əlaqəli olan `m²_qiyməti` və `Valyuta` sütunları model girişindən çıxarılmışdır.

## Preprocessing

Modelin öyrədilməsindən əvvəl məlumatlar train və test hissələrinə bölünmüşdür. Data leakage probleminin qarşısını almaq üçün preprocessing və model mərhələləri `Pipeline` daxilində birləşdirilmişdir.

Preprocessing mərhələsində:

- Numerik dəyişənlər ayrıca emal edilmişdir.
- Kateqorik dəyişənlər `OneHotEncoder` vasitəsilə ədədi formaya çevrilmişdir.
- `ColumnTransformer` vasitəsilə numerik və kateqorik dəyişənlər birlikdə emal edilmişdir.
- Çatışmayan dəyərlərin idarə olunması preprocessing pipeline daxilində həyata keçirilmişdir.

## İstifadə olunan modellər

### 1. Linear Regression

İlk olaraq baza model kimi Linear Regression istifadə edilmişdir.

Nəticələr:

- **MAE:** 169,631.31 AZN
- **RMSE:** 619,392.32 AZN
- **R²:** 0.3217

### 2. Random Forest Regressor

Qeyri-xətti əlaqələri və dəyişənlər arasındakı daha mürəkkəb münasibətləri öyrənmək üçün Random Forest Regressor tətbiq edilmişdir.

Nəticələr:

- **MAE:** 60,753.53 AZN
- **RMSE:** 343,135.80 AZN
- **R²:** 0.7909

### 3. Gradient Boosting Regressor

Alternativ ensemble yanaşması kimi Gradient Boosting Regressor istifadə edilmişdir.

Nəticələr:

- **MAE:** 88,328.44 AZN
- **RMSE:** 372,116.60 AZN
- **R²:** 0.7534

## Model müqayisəsi

Modellər eyni **5-Fold Cross-Validation** metodologiyası və eyni MAE, RMSE və R² metrikləri ilə müqayisə edilmişdir.

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Linear Regression | 169,631.31 | 619,392.32 | 0.3217 |
| Random Forest | **60,753.53** | **343,135.80** | **0.7909** |
| Gradient Boosting | 88,328.44 | 372,116.60 | 0.7534 |

Nəticələr göstərmişdir ki, **Random Forest Regressor** digər modellərlə müqayisədə daha aşağı MAE və RMSE, daha yüksək R² göstəricisi əldə etmişdir. Buna görə hyperparameter tuning üçün Random Forest modeli seçilmişdir.

## Cross-Validation

Modellərin müxtəlif məlumat bölgülərində performansını yoxlamaq üçün **5-Fold Cross-Validation** tətbiq edilmişdir.

Random Forest modeli üçün orta CV nəticələri:

- **MAE:** 60,753.53 AZN
- **RMSE:** 343,135.80 AZN
- **R²:** 0.7909

Cross-Validation nəticələri modelin müxtəlif train-validation bölgülərində performansının müqayisə edilməsinə və model seçiminin daha etibarlı aparılmasına imkan vermişdir.

## Hyperparameter Tuning

Ən yaxşı nəticə göstərən Random Forest modelinin performansını yaxşılaşdırmaq üçün **RandomizedSearchCV** tətbiq edilmişdir.

Axtarılan əsas parametrlər:

- `n_estimators`
- `max_depth`
- `min_samples_leaf`
- `max_features`

Ən yaxşı parametr kombinasiyası:

```text
n_estimators = 100
max_depth = 20
min_samples_leaf = 1
max_features = 1.0
