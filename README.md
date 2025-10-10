# 📚 مستندات کامل API Controller - YoozMovie

## 🎬 نمای کلی
این API برای اپلیکیشن اندروید YoozMovie طراحی شده است و امکان دسترسی به اطلاعات فیلم‌ها، بازیگران، نظرات و سایر امکانات را فراهم می‌کند.

**Base URL:** `https://yoozmovie.com/Api/`

**CDN URL:** `https://cdn.yoozmovie.com`

---

## 📋 فهرست Endpoints

1. [Preflight (CORS)](#1-preflight-cors)
2. [دریافت لیست فیلم‌ها (با صفحه‌بندی)](#2-دریافت-لیست-فیلمها-با-صفحهبندی)
3. [دریافت جزئیات فیلم](#3-دریافت-جزئیات-فیلم)
4. [دریافت نظرات فیلم](#4-دریافت-نظرات-فیلم)
5. [ارسال نظر برای فیلم](#5-ارسال-نظر-برای-فیلم)
6. [لایک/دیس‌لایک نظر](#6-لایکدیسلایک-نظر)
7. [دریافت اطلاعات بازیگر](#7-دریافت-اطلاعات-بازیگر)
8. [دریافت فیلم‌های مرتبط](#8-دریافت-فیلمهای-مرتبط)

---

## 1. Preflight (CORS)

### 🔧 OPTIONS `/Api/Preflight`

پشتیبانی از CORS برای درخواست‌های Cross-Origin.

**Headers Response:**
- `Access-Control-Allow-Origin: *`
- `Access-Control-Allow-Methods: GET, POST, OPTIONS`
- `Access-Control-Allow-Headers: Content-Type, X-Requested-With`

**Status Code:** `200 OK`

---

## 2. دریافت لیست فیلم‌ها (با صفحه‌بندی)

### 📄 GET `/Api/Index`

دریافت لیست فیلم‌ها با قابلیت صفحه‌بندی برای Infinite Scrolling.

**Parameters:**

| Parameter | Type | Default | Required | Description |
|-----------|------|---------|----------|-------------|
| page | int | 1 | ❌ | شماره صفحه (حداقل: 1) |
| pageSize | int | 12 | ❌ | تعداد آیتم در هر صفحه (بین 1-50) |

**Example Request:**
```
GET /Api/Index?page=1&pageSize=12
```

**Response (200 OK):**
```json
{
  "Movies": [
    {
      "MovieId": 12345,
      "Name": "Inception",
      "NameFa": "تلقین",
      "Poster": "https://cdn.yoozmovie.com/posters/inception.jpg"
    }
  ],
  "Page": 1,
  "PageSize": 12,
  "TotalPages": 45,
  "TotalMovies": 540,
  "HasNextPage": true,
  "HasPreviousPage": false
}
```

**Use Case (Android):**
```kotlin
// صفحه اول
api.getMovies(page = 1, pageSize = 12)

// صفحه دوم (Infinite Scroll)
if (response.HasNextPage) {
    api.getMovies(page = 2, pageSize = 12)
}
```

---

## 3. دریافت جزئیات فیلم

### 🎥 GET `/Api/GetMovieDetails`

دریافت اطلاعات کامل یک فیلم شامل بازیگران، ژانرها، دانلودها و گالری.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | int | ✅ | شناسه فیلم (movieId) |

**Example Request:**
```
GET /Api/GetMovieDetails?id=12345
```

**Response (200 OK):**
```json
{
  "MovieId": 12345,
  "Name": "Inception",
  "NameFa": "تلقین",
  "Poster": "https://cdn.yoozmovie.com/posters/inception.jpg",
  "Backdrop": "https://cdn.yoozmovie.com/backdrops/inception.jpg",
  "Overview": "A thief who steals corporate secrets...",
  "OverviewFa": "دزدی که اسرار شرکتی را می‌دزد...",
  "Runtime": 148,
  "Year": 2010,
  "Rate": 8.8,
  "Free": 1,
  "Persian": 1,
  "Imdb": "tt1375666",
  "Age": "PG-13",
  "Player": "https://player.url/movie.m3u8",
  "TvodPrice": "5000",
  "Genres": [
    {
      "Id": 1,
      "NameFa": "اکشن",
      "NameEn": "Action"
    }
  ],
  "Cast": [
    {
      "Id": 100,
      "Name": "Leonardo DiCaprio",
      "Image": "https://cdn.yoozmovie.com/cast/leo.jpg"
    }
  ],
  "VideoGallery": [
    {
      "Id": 22074,
      "videoPoster": "https://cdn.yoozmovie.com/Trailer/poster.jpg",
      "LinkUrl": "https://cdn.yoozmovie.com/Trailer/trailer.mp4",
      "Name": "Official Trailer"
    }
  ],
  "photos": [
    {
      "Id": 500,
      "Title": "Scene 1",
      "Image": "https://cdn.yoozmovie.com/photos/scene1.jpg"
    }
  ],
  "Downloads": [
    {
      "Id": 1000,
      "Type": "BluRay",
      "LinkUrl": "https://download.url/movie.mp4",
      "Size": "1.5GB",
      "Title": "1080p BluRay",
      "Amount": 5000
    }
  ]
}
```

**Error Response (404):**
```json
{
  "error": "Movie not found"
}
```

---

## 4. دریافت نظرات فیلم

### 💬 GET `/Api/GetMovieReviews`

دریافت تمام نظرات یک فیلم به همراه تعداد لایک و دیس‌لایک.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| movieId | int | ✅ | شناسه فیلم |

**Example Request:**
```
GET /Api/GetMovieReviews?movieId=12345
```

**Response (200 OK):**
```json
[
  {
    "ReviewId": 501,
    "Rating": 5,
    "Comment": "فیلم فوق‌العاده‌ای بود!",
    "NameComment": "علی رضایی",
    "CreatedAt": "2024-01-15T10:30:00",
    "LikeCount": 25,
    "DislikeCount": 2
  },
  {
    "ReviewId": 502,
    "Rating": 4,
    "Comment": "داستان جالبی داشت",
    "NameComment": "سارا احمدی",
    "CreatedAt": "2024-01-14T15:20:00",
    "LikeCount": 10,
    "DislikeCount": 1
  }
]
```

**Notes:**
- نظرات به ترتیب جدیدترین نمایش داده می‌شوند
- `Rating` بین 1 تا 5 است

---

## 5. ارسال نظر برای فیلم

### ✍️ POST `/Api/SubmitReview`

ثبت نظر جدید برای یک فیلم.

**Parameters (Form Data):**

| Parameter | Type | Required | Validation | Description |
|-----------|------|----------|------------|-------------|
| movieId | int | ✅ | > 0 | شناسه فیلم |
| rating | int | ✅ | 1-5 | امتیاز فیلم |
| comment | string | ✅ | Not Empty | متن نظر |
| nameComment | string | ✅ | Not Empty | نام نظردهنده |
| userId | string | ❌ | - | شناسه کاربر (اختیاری) |

**Example Request:**
```
POST /Api/SubmitReview
Content-Type: application/x-www-form-urlencoded

movieId=12345&rating=5&comment=عالی بود!&nameComment=محمد&userId=user123
```

**Success Response (200 OK):**
```json
{
  "success": true,
  "message": "Review submitted successfully",
  "reviewId": 503
}
```

**Error Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "Rating must be between 1 and 5"
}
```

**Possible Error Messages:**
- `"Movie ID is invalid"`
- `"Rating must be between 1 and 5"`
- `"Comment is required"`
- `"Name is required"`
- `"Movie not found"`

**Additional Info:**
- IP کاربر به صورت خودکار ثبت می‌شود
- تاریخ ایجاد نظر به صورت خودکار تنظیم می‌شود

---

## 6. لایک/دیس‌لایک نظر

### 👍👎 POST `/Api/ReactToReview`

ثبت واکنش (لایک یا دیس‌لایک) به یک نظر.

**Parameters (Form Data):**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| reviewId | int | ✅ | شناسه نظر |
| userId | string | ✅ | شناسه کاربر (برای جلوگیری از تکرار) |
| isLike | bool | ✅ | true = لایک, false = دیس‌لایک |

**Example Request:**
```
POST /Api/ReactToReview
Content-Type: application/x-www-form-urlencoded

reviewId=503&userId=user123&isLike=true
```

**Success Response (200 OK):**
```json
{
  "success": true,
  "message": "Reaction recorded successfully",
  "likeCount": 26,
  "dislikeCount": 2
}
```

**Error Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "You have already reacted to this review"
}
```

**Business Logic:**
1. ✅ اگر کاربر قبلاً واکنش نشان نداده، واکنش جدید ثبت می‌شود
2. ✅ اگر کاربر همان واکنش را دوباره ارسال کند، خطا برمی‌گرداند
3. ✅ اگر کاربر بخواهد واکنش خود را تغییر دهد (لایک → دیس‌لایک یا برعکس)، واکنش قبلی به‌روز می‌شود

**Possible Error Messages:**
- `"Review ID is invalid"`
- `"User ID is required"`
- `"Review not found"`
- `"You have already reacted to this review"`

---

## 7. دریافت اطلاعات بازیگر

### 🎭 GET `/Api/GetCastDetails`

دریافت اطلاعات کامل یک بازیگر شامل بیوگرافی، فیلم‌ها و عکس‌ها.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | int | ✅ | شناسه بازیگر (CastId) |

**Example Request:**
```
GET /Api/GetCastDetails?id=100
```

**Response (200 OK):**
```json
{
  "CastId": 100,
  "Name": "Leonardo DiCaprio",
  "Image": "https://cdn.yoozmovie.com/cast/leo.jpg",
  "Born": "November 11, 1974",
  "BirthName": "Leonardo Wilhelm DiCaprio",
  "NickName": "Leo",
  "Height": "6' (1.83 m)",
  "Bio": "Few actors in the world have had a career quite as diverse...",
  "Spouse": "Not married",
  "Children": "None",
  "Parents": "George DiCaprio, Irmelin Indenbirken",
  "Relatives": "Adam Farrar (stepbrother)",
  "ImdbId": "nm0000138",
  "Movies": [
    {
      "MovieId": 12345,
      "Name": "Inception",
      "NameFa": "تلقین",
      "Poster": "https://cdn.yoozmovie.com/posters/inception.jpg",
      "Year": 2010,
      "Rate": 8.8
    }
  ],
  "Photos": [
    {
      "PhotoId": 700,
      "PhotoUrl": "https://cdn.yoozmovie.com/actors/leo/photo1.jpg",
      "PhotoName": "Red Carpet Event"
    }
  ]
}
```

**Error Response (404):**
```json
{
  "error": "Cast not found"
}
```

**Error Response (500):**
```json
{
  "error": "An error occurred: [error message]"
}
```

---

## 8. دریافت فیلم‌های مرتبط

### 🔗 GET `/Api/GetRelatedMovies`

دریافت فیلم‌های مرتبط با یک فیلم بر اساس ژانرهای مشترک.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| movieId | int | ✅ | شناسه فیلم |

**Example Request:**
```
GET /Api/GetRelatedMovies?movieId=12345
```

**Response (200 OK):**
```json
[
  {
    "MovieId": 12346,
    "Name": "Interstellar",
    "NameFa": "در میان ستارگان",
    "Poster": "https://cdn.yoozmovie.com/posters/interstellar.jpg",
    "Year": 2014,
    "Rate": 8.6
  },
  {
    "MovieId": 12347,
    "Name": "The Prestige",
    "NameFa": "پرستیژ",
    "Poster": "https://cdn.yoozmovie.com/posters/prestige.jpg",
    "Year": 2006,
    "Rate": 8.5
  }
]
```

**Logic:**
- فیلم‌هایی که حداقل یک ژانر مشترک دارند
- مرتب‌سازی بر اساس بالاترین امتیاز
- حداکثر 6 فیلم برمی‌گرداند
- فیلم فعلی از لیست حذف می‌شود

---

## 9. جستجوی فیلم‌ها

### 🔎 GET `/Api/Search`

جستجو در فیلم‌ها بر اساس نام فارسی یا انگلیسی با قابلیت صفحه‌بندی.

**Parameters (Query String):**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| q | string | ✅ | - | کوئری جستجو (نام فیلم به فارسی یا انگلیسی) |
| page | int | ❌ | 1 | شماره صفحه مورد نظر |
| pageSize | int | ❌ | 18 | تعداد نتایج در هر صفحه (حداکثر: 50) |

**Example Requests:**

```
# جستجوی ساده
GET /Api/Search?q=avengers

# جستجو با صفحه‌بندی
GET /Api/Search?q=spider&page=2&pageSize=12

# جستجوی فارسی
GET /Api/Search?q=انتقام‌جویان

# جستجو با تنظیمات کامل
GET /Api/Search?q=batman&page=1&pageSize=20
```

**Success Response (200 OK):**

```json
{
  "Success": true,
  "SearchQuery": "avengers",
  "Movies": [
    {
      "MovieId": 123,
      "Name": "The-Avengers",
      "NameFa": "انتقام‌جویان",
      "Poster": "https://cdn.yoozmovie.com/posters/avengers.jpg",
      "Year": 2012,
      "Rate": 8.0,
      "Imdb": "tt0848228"
    },
    {
      "MovieId": 456,
      "Name": "Avengers-Age-of-Ultron",
      "NameFa": "انتقام‌جویان: عصر اولترون",
      "Poster": "https://cdn.yoozmovie.com/posters/avengers2.jpg",
      "Year": 2015,
      "Rate": 7.3,
      "Imdb": "tt2395427"
    }
  ],
  "Page": 1,
  "PageSize": 18,
  "TotalPages": 3,
  "TotalMovies": 47,
  "HasNextPage": true,
  "HasPreviousPage": false
}
```

**Empty Results Response (200 OK):**

```json
{
  "Success": true,
  "SearchQuery": "nonexistentmovie",
  "Movies": [],
  "Page": 1,
  "PageSize": 18,
  "TotalPages": 0,
  "TotalMovies": 0,
  "HasNextPage": false,
  "HasPreviousPage": false
}
```

**Error Response (400 Bad Request):**

```json
{
  "success": false,
  "message": "Search query is required"
}
```

**Server Error Response (500 Internal Server Error):**

```json
{
  "success": false,
  "message": "An error occurred: [error details]"
}
```

**Response Fields:**

| Field | Type | Description |
|-------|------|-------------|
| Success | bool | وضعیت موفقیت درخواست |
| SearchQuery | string | کوئری جستجوی دریافت شده |
| Movies | array | آرایه‌ای از فیلم‌های یافت شده |
| Movies[].MovieId | int | شناسه یکتای فیلم |
| Movies[].Name | string | نام انگلیسی فیلم (با خط تیره برای URL) |
| Movies[].NameFa | string | نام فارسی فیلم |
| Movies[].Poster | string | URL کامل پوستر فیلم |
| Movies[].Year | int | سال تولید فیلم |
| Movies[].Rate | double | امتیاز IMDB |
| Movies[].Imdb | string | شناسه IMDB فیلم |
| Page | int | شماره صفحه فعلی |
| PageSize | int | تعداد نتایج در هر صفحه |
| TotalPages | int | تعداد کل صفحات |
| TotalMovies | int | تعداد کل فیلم‌های یافت شده |
| HasNextPage | bool | آیا صفحه بعدی وجود دارد؟ |
| HasPreviousPage | bool | آیا صفحه قبلی وجود دارد؟ |

**Business Logic:**

1. ✅ جستجو در هر دو فیلد `name` (انگلیسی) و `name_fa` (فارسی) انجام می‌شود
2. ✅ فقط فیلم‌هایی که `SendMovie != null` هستند نمایش داده می‌شوند
3. ✅ نتایج بر اساس `movieId` به صورت نزولی مرتب می‌شوند (جدیدترین‌ها اول)
4. ✅ اعتبارسنجی پارامترها:
   - `page` باید بزرگتر از 0 باشد (پیش‌فرض: 1)
   - `pageSize` بین 1 تا 50 (پیش‌فرض: 18)
   - `q` نمی‌تواند خالی باشد
5. ✅ در صورت عدم یافتن نتیجه، آرایه `Movies` خالی برگردانده می‌شود

**Possible Error Messages:**

- `"Search query is required"` - کوئری جستجو خالی است
- `"An error occurred: [details]"` - خطای سرور

**Use Cases:**

- 🎬 جستجوی سریع فیلم در وب‌سایت
- 📱 اپلیکیشن‌های موبایل (iOS/Android)
- 💡 پیشنهاد خودکار (Autocomplete) هنگام تایپ
- 🔗 ویجت جستجو در سایت‌های شخص ثالث
- 📊 تحلیل و آمار جستجوها

---

## 🔐 Authentication & Security

### IP Tracking
- در اکشن `SubmitReview`، IP کاربر با `Request.UserHostAddress` ذخیره می‌شود

### User Tracking
- در اکشن `ReactToReview`، از `userId` برای جلوگیری از واکنش تکراری استفاده می‌شود
- هر کاربر فقط یک بار می‌تواند به یک نظر واکنش نشان دهد

### CORS Headers
- تمام Origin ها مجاز هستند (`*`)
- متدهای مجاز: `GET, POST, OPTIONS`
- Header های مجاز: `Content-Type, X-Requested-With`

---

## 📊 Data Models

### Movie Object
```typescript
interface Movie {
  MovieId: number;
  Name: string;
  NameFa: string;
  Poster: string;
  Backdrop?: string;
  Overview?: string;
  OverviewFa?: string;
  Runtime?: number;
  Year: number;
  Rate: number;
  Free?: number;
  Persian?: number;
  Imdb?: string;
  Age?: string;
  Player?: string;
  TvodPrice?: string;
}
```

### Review Object
```typescript
interface Review {
  ReviewId: number;
  Rating: number; // 1-5
  Comment: string;
  NameComment: string;
  CreatedAt: string; // ISO 8601
  LikeCount: number;
  DislikeCount: number;
}
```

### Cast Object
```typescript
interface Cast {
  CastId: number;
  Name: string;
  Image: string;
  Born?: string;
  BirthName?: string;
  NickName?: string;
  Height?: string;
  Bio?: string;
  Spouse?: string;
  Children?: string;
  Parents?: string;
  Relatives?: string;
  ImdbId?: string;
}
```

---

## ⚙️ Technical Details

### Framework
- **ASP.NET MVC** (.NET Framework 4.7.2)
- **Entity Framework** for data access
- **C# 7.3**

### Database Context
```csharp
ApplicationDbContext
- movies (DbSet<movie>)
- Review (DbSet<Review>)
- Reactions (DbSet<Reaction>)
- cast (DbSet<Cast>)
- GenereVaset (DbSet<GenereVasets>)
```

### Response Format
- همه پاسخ‌ها به صورت **JSON** هستند
- استفاده از `JsonRequestBehavior.AllowGet` برای GET requests

---

## 🚀 نمونه کد Android (Kotlin)

### Retrofit Interface
```kotlin
interface YoozMovieApi {
    @GET("Api/Index")
    suspend fun getMovies(
        @Query("page") page: Int = 1,
        @Query("pageSize") pageSize: Int = 12
    ): MovieListResponse

    @GET("Api/GetMovieDetails")
    suspend fun getMovieDetails(
        @Query("id") id: Int
    ): MovieDetailsResponse

    @GET("Api/GetMovieReviews")
    suspend fun getMovieReviews(
        @Query("movieId") movieId: Int
    ): List<ReviewResponse>

    @FormUrlEncoded
    @POST("Api/SubmitReview")
    suspend fun submitReview(
        @Field("movieId") movieId: Int,
        @Field("rating") rating: Int,
        @Field("comment") comment: String,
        @Field("nameComment") nameComment: String,
        @Field("userId") userId: String?
    ): SubmitReviewResponse

    @FormUrlEncoded
    @POST("Api/ReactToReview")
    suspend fun reactToReview(
        @Field("reviewId") reviewId: Int,
        @Field("userId") userId: String,
        @Field("isLike") isLike: Boolean
    ): ReactToReviewResponse

    @GET("Api/GetCastDetails")
    suspend fun getCastDetails(
        @Query("id") id: Int
    ): CastDetailsResponse

    @GET("Api/GetRelatedMovies")
    suspend fun getRelatedMovies(
        @Query("movieId") movieId: Int
    ): List<MovieResponse>
}
```

### نمونه استفاده
```kotlin
// دریافت فیلم‌ها با Infinite Scroll
viewModelScope.launch {
    try {
        val response = api.getMovies(page = currentPage, pageSize = 12)
        if (response.HasNextPage) {
            currentPage++
        }
        moviesList.addAll(response.Movies)
    } catch (e: Exception) {
        // Handle error
    }
}

// ارسال نظر
viewModelScope.launch {
    val result = api.submitReview(
        movieId = movieId,
        rating = 5,
        comment = "فیلم عالی بود!",
        nameComment = userName,
        userId = userId
    )
    if (result.success) {
        // نظر با موفقیت ثبت شد
    }
}
```

---

## 📝 Notes & Best Practices

### Pagination
- برای اپلیکیشن‌های موبایل، `pageSize=12` پیشنهاد می‌شود
- حداکثر `pageSize` برابر 50 است

### Error Handling
- همیشه `success` یا `error` را در Response چک کنید
- در صورت خطا، `message` یا `error` نمایش داده شود

### Performance
- از `.AsNoTracking()` برای بهبود عملکرد استفاده شود (در نسخه‌های بعدی)
- تصاویر از CDN سرو می‌شوند

### Validation
- تمام Input ها اعتبارسنجی می‌شوند
- از SQL Injection محافظت شده (Entity Framework)

---

## 📞 Support

برای پشتیبانی و گزارش مشکلات:
- **Website:** https://yoozmovie.com
- **GitHub:** https://github.com/designrco/yoozmovieCMS

---

**نسخه:** 1.0  
**آخرین به‌روزرسانی:** 2024  
**زبان برنامه‌نویسی:** C# 7.3  
**Framework:** ASP.NET MVC (.NET Framework 4.7.2)
