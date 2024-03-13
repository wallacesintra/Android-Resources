# **Convert JSON to data**

resources:

* <www.delasign.com/blog/android-studio-kotlin-convert-json-to-data/>

steps:

## 1.Create the JSON

creating a JSON

* create an Assets Folder, In the Project Inspector, right click the app folder and select New > Folder > Asset Folder.
* create the JSON file, right click on the Assets folder and select New>File, name your file(i.e sample.json)

## 2.Create the data class file

create a new file named after the class that you wish to associate with the JSON.

we have called it UIContent.kt, as we will use it to read a language JSON that will be used to populate UI strings.

## 3.Create JSON Data class

create a data class that matches the JSON you wish to convert into usable data.

```kotlin
data class UIContent(
    val sample: Sample,
    val sampleTwo: SampleTwo
) {
    data class Sample(
        val sampleString: String
    )

    data class SampleTwo(
        val sampleA: SampleSecondTierStructure,
        val sampleB: ASecondSampleSecondTierStructure
    ) {
        data class SampleSecondTierStructure(
            val sampleString: String,
            val sampleStringTwo: String
        )

        data class ASecondSampleSecondTierStructure(
            val aSampleString: String,
            val aSampleStringTwo: String
        )
    }
}
```

## 4.Create the convert JSON to string functionality

* create read JSON utility in a file.

```kotlin
import android.content.Context
import android.util.Log
import com.delasign.samplestarterproject.models.constants.DebuggingIdentifiers
import java.io.BufferedReader
import java.io.InputStreamReader

fun ReadJSONFromAssets(context: Context, path: String): String {
    val identifier = "[ReadJSON]"
    try {
        val file = context.assets.open("$path")
        Log.i(
            identifier,
            "${DebuggingIdentifiers.actionOrEventSucceded} Found File: $file.",
        )
        val bufferedReader = BufferedReader(InputStreamReader(file))
        val stringBuilder = StringBuilder()
        bufferedReader.useLines { lines ->
            lines.forEach {
                stringBuilder.append(it)
            }
        }
        Log.i(
            identifier,
            "getJSON  ${DebuggingIdentifiers.actionOrEventSucceded} stringBuilder: $stringBuilder.",
        )
        val jsonString = stringBuilder.toString()
        Log.i(
            identifier,
            "${DebuggingIdentifiers.actionOrEventSucceded} JSON as String: $jsonString.",
        )
        return jsonString
    } catch (e: Exception) {
        Log.e(
            identifier,
            "${DebuggingIdentifiers.actionOrEventFailed} Error reading JSON: $e.",
        )
        e.printStackTrace()
        return ""
    }
}
```

* read the JSON, Call ReadJSONFromAssets by passing an activity or composable's context and the name of the JSON file.

## 5.Add Gson to project

need Gson library

* Navigate to your app level build gradle (i.e. build.gradle.kt (Module :app)) and add the following line to the dependencies
* add `implementation("com.google.code.gson:gson:2.8.9")` or the latest version.

## 6.Convert the JSON string to data

convert the JSON string gathered from JSON to string functionality.

```kotlin
val data = Gson().fromJson(jsonString, UIContent::class.java)
```

## 7.Implement the data

To use the data, use dot notation with parameters that match the class that you created in Step Three (i.e. data.sample.sampleString or data.sampleTwo.sampleB.aSampleString).
