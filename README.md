# India Corona Tracker

## Overview
India Corona Tracker is a Java-based Android application that fetches real-time COVID-19 data from an external API and displays it on the user interface. The application presents both cumulative and state-wise COVID-19 statistics, including confirmed cases, active cases, recovered cases, and deaths.

## Features
- Fetches real-time COVID-19 data from an external API (`https://api.covid19india.org/data.json`).
- Displays overall COVID-19 statistics for India.
- Shows state-wise COVID-19 statistics in a list format.
- Uses `OkHttpClient` for API requests.
- Implements `Gson` for JSON parsing.
- Uses Kotlin Coroutines (`GlobalScope.launch`) for handling network calls asynchronously.

## Tech Stack
- **Language:** Java, Kotlin
- **Network Library:** OkHttp
- **JSON Parsing:** Gson
- **Concurrency:** Kotlin Coroutines
- **UI Components:** ListView, TextView

## Installation & Setup
1. Clone the repository:
   ```sh
   git clone https://github.com/shashank11yadav/India-Corona-Tracker.git
   ```
2. Open the project in **Android Studio**.
3. Ensure you have an active internet connection for fetching data.
4. Run the application on an emulator or a physical device.

## Code Structure
### 1. API Client (`Client.kt`)
Handles network requests using `OkHttpClient`.
```kotlin
object Client {
    private val okHttpClient = OkHttpClient()
    private val request = Request.Builder()
        .url("https://api.covid19india.org/data.json").build()
    val api = okHttpClient.newCall(request)
}
```

### 2. Main Activity (`MainActivity.kt`)
- Fetches COVID-19 data using coroutines.
- Parses JSON response using `Gson`.
- Displays the fetched data in `ListView`.
```kotlin
GlobalScope.launch {
    val response = withContext(Dispatchers.IO) { Client.api.execute() }
    if (response.isSuccessful) {
        val data = Gson().fromJson(response.body?.string(), Response::class.java)
        launch(Dispatchers.Main) {
            bindCombinedData(data.statewise[0])
            bindStateWiseData(data.statewise.subList(0, data.statewise.size))
        }
    }
}
```

### 3. List Adapter (`ListAdapter.kt`)
- Populates the state-wise COVID-19 data into a `ListView`.
```kotlin
class ListAdapter(val list: List<StatewiseItem>) : BaseAdapter() {
    override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {
        val view = convertView ?: LayoutInflater.from(parent?.context)
            .inflate(R.layout.item_list, parent, false)
        val item = list[position]
        view.confirmedTv.text = item.confirmed
        view.activeTv.text = item.active
        view.recoveredTv.text = item.recovered
        view.deceasedTv.text = item.deaths
        view.stateTv.text = item.state
        return view
    }
}
```

### 4. Data Models (`Response.kt`)
Defines the structure of API response data.
```kotlin
data class Response(
    val statewise: List<StatewiseItem>
)

data class StatewiseItem(
    val recovered: String? = null,
    val delta: Delta? = null,
    val active: String? = null,
    val state: String? = null,
    val confirmed: String? = null,
    val deaths: String? = null
)

data class Delta(
    val recovered: Int? = null,
    val active: Int? = null,
    val confirmed: Int? = null,
    val deaths: Int? = null
)
```
