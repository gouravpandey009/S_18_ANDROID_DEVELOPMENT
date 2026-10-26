# S_18_ANDROID_DEVELOPMENT

# S18 — RecyclerView, Adapter & ViewHolder Deep Dive
### Android Development | Kotlin + XML Views

This session explains how Android displays dynamic lists efficiently using `RecyclerView`, a `LayoutManager`, an `Adapter`, and `ViewHolder` objects. The examples use **Kotlin + XML Views**.

## Learning Outcomes

By the end of this session, you should be able to:

- Explain why dynamic lists need a reusable list component.
- Describe the role and architecture of `RecyclerView`.
- Explain what a `LayoutManager`, `Adapter`, and `ViewHolder` do.
- Create an XML layout for one list item.
- Connect a data list to `RecyclerView`.
- Explain data binding and view recycling.
- Identify basic practices that help list performance.

## 1. Why Do We Need Dynamic Lists?

Many apps display collections of information:

- Student records
- Courses and lessons
- Messages
- Products
- Contacts
- Notifications

The number of items may change, and each item may contain text, images, or actions. Creating a separate view manually for every item is repetitive and can use unnecessary resources.

**The idea:** provide the data, define how one item looks, and let `RecyclerView` display and reuse item views as needed.

## 2. What Is RecyclerView?

`RecyclerView` is an AndroidX UI component for displaying a collection of items. It is designed to reuse item views instead of creating a new view for every item as the user scrolls.

Before using it, make sure the project includes the AndroidX RecyclerView dependency. The exact dependency version should match the project's current AndroidX setup.

## 3. RecyclerView Architecture

Think of the list as four connected parts:

```text
Data List
   |
   v
Adapter  <---- connects data to item views
   |
   v
ViewHolder <--- holds references to views in one item
   |
   v
RecyclerView <--- displays and scrolls the collection
   |
   v
LayoutManager <--- decides item arrangement and scrolling layout
```

### Responsibilities

| Component | Responsibility |
|---|---|
| `RecyclerView` | Container that displays the collection and manages scrolling/recycling. |
| `Adapter` | Creates item views and binds the relevant data to them. |
| `ViewHolder` | Holds references to the views belonging to one item view. |
| `LayoutManager` | Controls how items are arranged, such as a vertical list or grid. |
| Data source | The collection of records the adapter displays. |
| Item XML | Defines the appearance of one row/card. |

## 4. LayoutManager Concept

A `LayoutManager` controls the arrangement of items.

Common choices:

- `LinearLayoutManager`: one-dimensional vertical or horizontal list.
- `GridLayoutManager`: grid with a defined number of columns or rows, depending on orientation.
- `StaggeredGridLayoutManager`: grid where item sizes can vary.

Example:

```kotlin
recyclerView.layoutManager = LinearLayoutManager(this)
```

For a vertical list, `LinearLayoutManager(this)` is a common starting point. A grid can be configured like this:

```kotlin
recyclerView.layoutManager = GridLayoutManager(this, 2)
```

The `2` specifies two columns for the usual vertical grid configuration.

## 5. Adapter Pattern

The Adapter connects the data source to the item views. It tells `RecyclerView` how many items exist, how to create an item's view holder, and how to bind data to that holder.

Important methods:

- `getItemCount()` — number of items in the data set.
- `onCreateViewHolder()` — creates a holder and inflates the item layout.
- `onBindViewHolder()` — fills the holder's views with data for a particular position.

## 6. ViewHolder Concept

A `ViewHolder` represents one item's view and keeps references to its child views. `RecyclerView` reuses holders as items move on and off the screen.

Without proper reuse, repeatedly finding views and creating new item views can add unnecessary work during scrolling. `RecyclerView.Adapter` works with `ViewHolder` objects as part of its recycling mechanism.

## 7. Item Layout Structure

Create a file named `res/layout/item_student.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <TextView
        android:id="@+id/tvStudentName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Student Name"
        android:textSize="18sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/tvStudentCourse"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Course" />
</LinearLayout>
```

This layout describes **one student row**, not the entire list.

## 8. Complete Beginner Example

This example displays a list of student names and courses. It uses `findViewById` to keep the code beginner-friendly. Ensure the RecyclerView dependency is available and add the necessary imports in Android Studio.

### Step A — Activity layout

Create `res/layout/activity_main.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.recyclerview.widget.RecyclerView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/recyclerStudents"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="8dp"
    android:clipToPadding="false" />
```

### Step B — Data model

Create `Student.kt`:

```kotlin
data class Student(
    val name: String,
    val course: String
)
```

A data class is a simple way to represent each student record.

### Step C — Adapter and ViewHolder

Create `StudentAdapter.kt`:

```kotlin
class StudentAdapter(
    private val students: List<Student>
) : RecyclerView.Adapter<StudentAdapter.StudentViewHolder>() {

    class StudentViewHolder(itemView: View) :
        RecyclerView.ViewHolder(itemView) {
        val nameText: TextView = itemView.findViewById(R.id.tvStudentName)
        val courseText: TextView = itemView.findViewById(R.id.tvStudentCourse)
    }

    override fun onCreateViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): StudentViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_student, parent, false)
        return StudentViewHolder(view)
    }

    override fun onBindViewHolder(
        holder: StudentViewHolder,
        position: Int
    ) {
        val student = students[position]
        holder.nameText.text = student.name
        holder.courseText.text = student.course
    }

    override fun getItemCount(): Int {
        return students.size
    }
}
```

### Step D — Connect RecyclerView in MainActivity

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val students = listOf(
            Student("Asha", "Computer Science"),
            Student("Rahul", "Information Technology"),
            Student("Meera", "Electronics"),
            Student("Arjun", "Computer Science"),
            Student("Priya", "Information Technology")
        )

        val recyclerView =
            findViewById<RecyclerView>(R.id.recyclerStudents)

        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = StudentAdapter(students)
    }
}
```

### Imports to check

Android Studio can usually add imports automatically. Common imports include:

```kotlin
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.recyclerview.widget.LinearLayoutManager
import androidx.recyclerview.widget.RecyclerView
```

Keep the app's package declaration at the top of each Kotlin file. The `Student` model should be in the same package or imported from its actual package.

## 9. Data Binding Flow

For each visible item, the flow is:

1. The data list contains student records.
2. `getItemCount()` tells `RecyclerView` how many records are available.
3. `onCreateViewHolder()` inflates `item_student.xml` when a new holder is needed.
4. `onBindViewHolder()` takes the record for a position.
5. The holder's `TextView`s receive the student's name and course.
6. When scrolling, holders can be reused and bound to other records.

Example: when the adapter binds position `2`, it displays `students[2]` in that holder. When a holder is reused, the adapter must bind all the data needed for that item so old content does not remain visible.

## 10. RecyclerView Performance & Recycling

`RecyclerView` improves list efficiency by reusing item views. Only a portion of a long list is visible at once, so recycling helps avoid creating a brand-new view for every item as the user scrolls.

### Good practices

- Keep `onBindViewHolder()` focused on binding data.
- Avoid expensive operations such as network requests inside `onBindViewHolder()`.
- Do not create a new adapter repeatedly for every scroll event.
- Bind all fields each time so recycled rows do not show stale values.
- Use appropriately sized item layouts.
- For changing data sets, use suitable adapter update methods; `ListAdapter` and `DiffUtil` are useful follow-up topics.
- Test scrolling with a longer data list.

### Common mistakes

- Forgetting to set a `LayoutManager`.
- Using an item ID in Kotlin that is missing from the XML.
- Inflating the wrong layout resource.
- Forgetting to override `getItemCount()`.
- Using the wrong list position or binding the wrong record.
- Doing heavy work inside `onBindViewHolder()`.
- Updating the data collection without notifying or updating the adapter appropriately.

## 11. Practice Tasks

### Task 1 — Course List
Create a `Course` data class with `title` and `duration`, then show at least five courses.

### Task 2 — Change Layout
Switch from a vertical `LinearLayoutManager` to a two-column `GridLayoutManager`.

### Task 3 — Add a Third Field
Add a student ID to the data class and item XML, then bind it in the adapter.

### Task 4 — Larger List
Add at least 30 records and scroll through them. Observe that `RecyclerView` reuses item views.

### Task 5 — Explain the Flow
Explain, in your own words, what happens in `onCreateViewHolder()` versus `onBindViewHolder()`.

## 12. Viva & Interview Questions

1. Why is `RecyclerView` useful for dynamic lists?
2. What are the main parts of RecyclerView architecture?
3. What does a `LayoutManager` do?
4. What is the responsibility of an Adapter?
5. What is a ViewHolder?
6. What is the difference between `onCreateViewHolder()` and `onBindViewHolder()`?
7. What does `getItemCount()` return?
8. Why is an item XML layout separate from the Activity layout?
9. How does view recycling help performance?
10. Why should heavy operations be avoided inside `onBindViewHolder()`?
11. When might you use `GridLayoutManager`?
12. What could go wrong if recycled views are not fully rebound?

## Final Checklist

- [ ] RecyclerView dependency is configured.
- [ ] The activity layout contains a RecyclerView.
- [ ] An item XML layout defines one row.
- [ ] The data model represents one record.
- [ ] The adapter implements `onCreateViewHolder()`, `onBindViewHolder()`, and `getItemCount()`.
- [ ] The ViewHolder holds references to item views.
- [ ] A LayoutManager is assigned.
- [ ] The list displays the correct data.
- [ ] Scrolling works and rows do not display stale content.

**Key takeaway:** `RecyclerView` displays the collection, the `LayoutManager` arranges it, the `Adapter` connects data to views, and the `ViewHolder` holds references to the views that can be reused.
