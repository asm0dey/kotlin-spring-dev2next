---
theme: apple-basic
title: Advanced Kotlin Techniques for Spring Developers
titleTemplate: '%s — dev2next 2026'
colorSchema: dark
lineNumbers: false
routerMode: history
selectable: true
duration: 50min
drawings:
  persist: false
canvasWidth: 800
layout: intro
---

# Advanced Kotlin Techniques for Spring Developers

## Pasha Finkelshteyn <logos-kotlin-icon /><logos-spring-icon />

<div class="mt-8 text-sm opacity-60"><a href="https://asm0dey.site">asm0dey.site</a> | #dev2next | #kotlin</div>

<!--
title card is already up as the room settles. Do not speak over it.
-->

---
layout: image
image: /slide-2024-jsr305.png
---

<!--
No greeting. No name. Nothing.

Let it sit for a full three seconds before speaking.

"If you write Kotlin with Spring, you have seen this slide. Maybe from me.
This is the single most repeated piece of Kotlin-and-Spring advice of the last
four years: turn on strict JSR-305, and Spring's nullability annotations start
meaning something to the Kotlin compiler."

-->

---
layout: statement
---

# Who writes Spring in Kotlin?

<v-click>

## Is this flag in a build file you own?

</v-click>

<!--
"Hands up if you write Spring in Kotlin. At work, or at night, both count."

Count aloud: M. If the room is mostly Java: "Good. Everything today is about
what Spring took off Kotlin's plate, and half of it applies to you too."

[click] Make them commit.

"Keep it up only if that flag is in a build file you own right now."

Count aloud again: N. An actual number, not "a lot of you".
Then WAIT. Do not resolve it. Let them sit in the yes.

"Keep that hand up for a second. I want you to remember you raised it."
-->

---

# It doesn't do anything anymore

<div class="text-xl opacity-60 -mt-2"><code>-Xjsr305=strict</code></div>

<div class="text-sm opacity-50 mb-5">
its job: make Spring's Java API null-safe in your Kotlin — at <b>compile time</b>
</div>

<v-click>

> Spring Framework provides null-safety for its entire API using **JSpecify** annotations.

</v-click>

<v-click>

> As of **Kotlin 2.1**, Kotlin strictly enforces nullability annotations from the `org.jspecify.annotations` package.

</v-click>

<v-click>

<div class="text-xs opacity-40 mt-2">Spring Framework reference &mdash; Kotlin null-safety</div>

<div class="mt-6 text-3xl">Not wrong. <span style="color:#fb923c">Unemployed.</span></div>

</v-click>

<!--
[click] "Spring Framework 7 annotates its entire API with JSpecify.
Not JSR-305. JSpecify."

[click] "And since Kotlin 2.1, the compiler enforces JSpecify annotations
strictly, on its own, with no flag."

(beat)

[click] "So the flag is not wrong. It is not harmful. It is unemployed. It is doing a
job that the compiler and the framework now do without being asked."

The doc quotes are the proof. Do not paraphrase them.

IF ASKED "wasn't that flag what made request validation work?" — it was not,
and this is worth being precise about because half the blog posts filed it
under validation:

  -Xjsr305=strict is COMPILE TIME. It makes Kotlin read the JSR-305
  annotations on Spring's Java API so its types stop being platform types.
  It governs YOUR code compiling against SPRING'S API.

  The 400 on a missing "name" is RUNTIME, and it is jackson-module-kotlin.
  It reads Kotlin's own @Metadata, sees a non-nullable constructor parameter
  with no value, and throws. It never needed the flag, and it still works
  today without it.

Two different mechanisms that happened to arrive in the same tutorial. Say it
as a common mix-up, not as a confession.
-->

---

# And the old annotations are deprecated

````md magic-move
```kotlin
import org.springframework.lang.Nullable
```
```kotlin
import org.jspecify.annotations.Nullable
```
````

<v-click>

Spring's applied to **fields, params, return values**.

JSpecify applies to **type usage** — so it can say `List<@Nullable String>` and mean it.

</v-click>

<!--
"The org.springframework.lang annotations are deprecated in favour of
JSpecify."

[click] "And this is not a rename. Spring's annotations applied to a field, a
parameter, a return value. JSpecify annotations apply to a type usage."

[click] "Which means JSpecify can express a non-null list of nullable strings,
and the old ones could not. That is a real gain, and you get it by deleting a
line, not adding one."
-->

---

# That build file doesn't even compile

```kotlin {all}
// 2024 — removed in Kotlin 2.2
tasks.withType<KotlinCompile> {
  kotlinOptions { jvmTarget = "21" }
}
```

<v-click>

```kotlin
// 2026
kotlin {
  compilerOptions {
    jvmTarget.set(JvmTarget.JVM_21)
  }
}
```

</v-click>

<div class="mt-4 text-xs opacity-50">

Source: https://kotlinlang.org/docs/gradle-compiler-options.html — deprecated in Kotlin 2.0, removed in 2.2
</div>

<!--
"There is a second thing wrong with that slide, and it is funnier.
kotlinOptions was deprecated in Kotlin 2.0 and removed in 2.2."

Flat, not apologetic. This is the material, not a defect.
Do NOT say sorry. Do NOT say "I forgot to update this".

[click] "So the slide I opened with does not merely give stale advice. Copy it
into a project today and it does not build."
-->

---
layout: statement
---

# Some of what you were taught was <span style="color:#4ade80">LEVERAGE</span>.

# Some of it was <span style="color:#fb923c">SCAFFOLDING</span>.

<div class="mt-8 text-2xl opacity-80">Today we sort them.</div>

<!--
"Here is what I actually want to give you today, and it is not a list of
techniques. Every Kotlin trick you use with Spring is one of two things."

"Leverage: something the language lets you do that no framework release can
make unnecessary. That compounds. You will still be using it in 2030."

"Scaffolding: something you built around a hole in the framework. That is a
loan. It comes due the release the hole gets filled, and you find out by
reading a migration guide."

"The flag was scaffolding. Everything we look at today, we sort."
-->

---
layout: image-right
image: /avatar.jpg
---

# `whoami`

**Pasha Finkelshteyn** · <a href="https://asm0dey.site" target="_blank">asm0dey.site</a>

<div class="mt-6 opacity-80">This advice was everywhere in 2022–24 — including in my own talks. Here is what changed.</div>

<div class="mt-5 text-sm opacity-60">
I work on <b>Liberica JDK</b> at BellSoft.
</div>

<div class="mt-2 text-sm opacity-60">
spring.io/quickstart: <i>"We recommend BellSoft Liberica JDK version 17 or 21."</i>
</div>

<!--
Twenty seconds. No credential slide.

"Twenty seconds on me, because you have already been given a reason to care
and that is the right order."

"Pasha. I work on Liberica JDK at BellSoft. I am not going to tell you it is
good — Spring already does. That line is on spring.io/quickstart, and it is
also the default runtime in Spring Boot's Paketo buildpacks, so if you have
ever built a Boot image without choosing a JDK, you have already run my day
job."

"That is the entire employer segment. Link is at the end if you want it; if
not, that is completely fine and it will not come up again."

Do not expand. Do not mention the product line. Move on.
-->

---

# One codebase, all session

```text {all|2|3|4|5|6}
sample/
  Person.kt          — the domain
  PersonRepo.kt      — JDBC + R2DBC
  PersonClient.kt    — HTTP
  Beans.kt           — registration + security
  build.gradle.kts   — Boot 4.1 / Kotlin 2.4 / Java 21
```

<!--
"One nano-service. Not six unrelated snippets — one codebase, and
every comparison today is a diff against it."

[click] The domain.
[click] One repository, two ways: JDBC and R2DBC.
[click] An HTTP client.
[click] Bean registration and security.
[click] The build file — next slide.
-->

---

# The build file, 2026

```kotlin {all|1-6|8-10|11}
plugins {
  kotlin("jvm") version "2.4.21"
  kotlin("plugin.spring") version "2.4.21"
  id("org.springframework.boot") version "4.1.1"
  id("io.spring.dependency-management") version "1.1.7"
}

kotlin {
  compilerOptions { jvmTarget.set(JvmTarget.JVM_21) }
}
// no -Xjsr305 anywhere
```

<!--

[click] plugins: "Two Kotlin plugins, both on the same version. Note that they
are on the same version."
  Dry. This is the ONLY reference to the 2024 version-mismatch
  slide, and it is made by the code, not by an apology.

[click] compilerOptions
[click] the comment: "And nothing where the flag used to be."
-->

---

# What we're sorting

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<span class="opacity-30 italic">— none yet —</span>
</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>


<v-click>

<div style="color:#fb923c">
-Xjsr305=strict<br/>
</div>

</v-click>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> extensions & varargs · coroutines · the bean DSL · the security DSL · HTTP clients · JSON · tests
</div>

<!--
This table returns at the end of every chapter with one
more row filled.

[click] "One row already filled. Seven to go. Every chapter ends here, and by the last
one you will have a rule you can apply to code I have never seen."
-->

---
layout: section
---

# Extension functions & varargs

## Leverage that never expires


---

# The query

```sql
SELECT *
FROM users
WHERE id = ?
```

<!--
"We need to run this. That is the entire requirement."
-->

---

# Before you say it's too simple

```sql {1-18|18-22}{maxHeight:'250px'}
SELECT DISTINCT book.id
              , (SELECT COALESCE(JSON_GROUP_ARRAY(JSON_ARRAY(t.v0, t.v1, t.v2, t.v3, t.v4, t.v5, t.v6, t.v7, t.v8,
                                                             t.v9)), JSON_ARRAY())
                 FROM (SELECT b.id AS v0 , b.path AS v1 , b.name AS v2 , b.date AS v3 , b.added AS v4 , b.sequence AS v5
                            , b.sequence_number AS v6 , b.lang AS v7 , b.zip_file AS v8 , b.seqid AS v9 FROM book AS b
                       WHERE b.id = book.id) AS t)                AS book
              , (SELECT COALESCE(JSON_GROUP_ARRAY(JSON_ARRAY(t.v0, t.v1, t.v2, t.v3, t.v4, t.v5, t.v6)), JSON_ARRAY())
                 FROM (SELECT DISTINCT author.id AS v0 , author.fb2id AS v1 , author.first_name AS v2
                                    , author.middle_name AS v3 , author.last_name AS v4 , author.nickname AS v5 , author.added AS v6
                       FROM author
                                JOIN book_author ON book_author.author_id = author.id
                       WHERE book_author.book_id = book.id) AS t) AS authors
              , (SELECT COALESCE(JSON_GROUP_ARRAY(JSON_ARRAY(t.v0, t.v1)), JSON_ARRAY())
                 FROM (SELECT DISTINCT genre.name AS v0, genre.id AS v1
                       FROM genre
                                JOIN book_genre ON book_genre.genre_id = genre.id
                       WHERE book_genre.book_id = book.id) AS t)  AS genres
              , book.sequence
FROM book
         JOIN book_author ON book_author.book_id = book.id
WHERE (book.seqid = 40792 AND book_author.author_id = 34606)
ORDER BY book.sequence_number ASC NULLS LAST, book.name
```

<!--
"Before anyone tells me a single-parameter query proves nothing: this is what
the queries in my own applications look like. The toy on the previous slide is
standing in for this. Everything that is annoying at one parameter is
unbearable at nine."

[click] (scrolls to the WHERE) "Two values. That is all this monster needs."

Costs ~40s. First cut if the opening overruns.
-->

---

# In Java

````md magic-move {lines: true}
```java
public List<Person> findById(int id) {
  return jdbcTemplate.query(
    "SELECT * FROM users WHERE id = ?",
    new UserRowMapper(), id);
}

private static class UserRowMapper implements RowMapper<Person> {
  @Override
  public Person mapRow(ResultSet rs, int i) throws SQLException {
    return new Person(rs.getInt("id"),
                      rs.getString("name"),
                      rs.getDouble("age"));
  }
}
```
```java
public List<Person> findById(int userId) {
  return jdbcTemplate.query(
    "SELECT * FROM users WHERE id = ?",
    (resultSet, i) -> {
      int id = resultSet.getInt("id");
      String name = resultSet.getString("name");
      double age = resultSet.getDouble("age");
      return new Person(id, name, age);
    },
    userId);      // ← the parameter, seven lines from its ?
}
```
````

<!--
"A whole class to turn a row into an object. So we inline it."
[click] "Better. And now look where userId ended up. Seven lines below the
question mark it fills."
-->

---

# Why Java has to do that

```java {all|4}
public <T> List<T> query(
    String sql,
    RowMapper<T> rowMapper,
    @Nullable Object... args)
```

<!--
"It is not Spring's fault and it is not a bad API."

[click] "In Java a vararg can only be
the last parameter. The mapper is not the last thing conceptually, but it has
to be second, because args has to be last."

(short pause — let anyone who wants to call out the Kotlin answer do it)
-->

---

# In Kotlin

```kotlin {all|1|2-6}
jdbcTemplate.query("SELECT * FROM users WHERE id = ?", userId) { rs, _ ->
  Person(
    rs.getInt("id"),
    rs.getString("name"),
    rs.getDouble("age"),
  )
}
```

<!--
[click] "Query, then its parameter, then the mapper as a block. Two things
make that legal, and neither of them is a Spring feature. A vararg does not
have to be last in Kotlin."

[click] "And a trailing lambda moves outside the parentheses, so the mapper reads as a body rather than an argument.
The underscore is the unused index."
-->

---

# The extension that does it

```kotlin {all|1|2|3|4}
fun <T> JdbcOperations.query(
  sql: String,
  vararg args: Any,
  function: (ResultSet, Int) -> T
): List<T>
```

<v-click>

<div class="mt-8 text-xl">Spring ships this. <b>You could have written it.</b></div>

</v-click>

<!--
"An extension function on the Java interface."

[click] The receiver: JdbcOperations.
[click] The SQL.
[click] The vararg — not last.
[click] The function — last, so it can trail.

[click] "Spring ships it, in spring-jdbc, and this is the part I want you to
take: there is nothing privileged about it."
-->

---

# Do it to your own dependencies

```java
// some library you don't control
public List<String> transformStrings(
    Function<String,String> mapper, String... args)
```

<v-click>

```kotlin
// one file in your codebase
fun StringUtils.transformStrings(
  vararg args: String,
  mapper: (String) -> String
): List<String>
```

</v-click>

<!--
"Some library you depend on and cannot change. Mapper first, varargs last."

[click] "You can do this to any Java API you depend on and cannot change. It is one
file. It does not require the library author's permission, a pull request, or
a fork."

"This is the single highest-value thing in this talk for a team that has a Java
dependency it hates."
-->

---

# Sorted: extensions & varargs

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>


<v-click>

<div style="color:#4ade80">
extension functions<br/>
vararg reordering<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
</div>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> coroutines · the bean DSL · the security DSL · HTTP clients · JSON · tests
</div>

<v-click>

<div class="mt-4 text-center text-xl">Could a framework release make this unnecessary?<br/>No. So it compounds.</div>

</v-click>

<!--
Ask BEFORE revealing the tick.

"Leverage or scaffolding?"
(wait for the call — this one is easy and they should get it right,
which is the point)

[click] "Leverage."

[click] "And here is the test that gets you there without me: could a
framework release make this unnecessary? No. So it compounds."
-->

---
layout: section
---

# Coroutines

## Then and now


---
layout: two-cols-header
---

# The monadic tax

::left::

```kotlin
fun findUser(id: Long): Mono<User>
fun findAll(): Flux<User>
```

<v-click>

```kotlin
suspend fun findUser(id: Long): User
fun findAll(): Flow<User>
```

</v-click>

::right::

<img src="/monadic-tax.png" class="w-full ml-8 mt-6"/>

<!--
"The 2024 argument, and I still stand behind it. Reactive gives you non-blocking and back-pressure, and charges you a monad for it. Every return type is a box, and once one function is boxed, its callers are."

[click] "Coroutines get you the same non-blocking behaviour and hand back ordinary types. User. List. Flow, which is the one box that remains, and it is a collection you can iterate."
-->

---

# The whole mapping

<v-clicks>

| reactive | coroutine |
|---|---|
| `Mono<Void>` | `suspend fun handler()` |
| `Mono<T>` | `suspend fun handler(): T` |
| `Flux<T>` | `fun handler(): Flow<T>` |

</v-clicks>

<v-click>

<div class="mt-8">Flow is push-based; back-pressure is the suspension itself.</div>

</v-click>

<!--
[click] Mono<Void> becomes a suspend fun.
[click] Mono<T> becomes a suspend fun returning T.
[click] Flux<T> becomes a function returning Flow<T>.

"That is the entire translation table. Three rows. If you can read a
reactive signature you can now read a coroutine one."

[click] "One real difference: Flux is a push-pull hybrid with an explicit
request protocol. Flow is push-based, and back-pressure is just the consumer
suspending. Simpler, and it is the reason map can be suspending in Flow and
needs to be flatMap in Flux."
-->

---

# R2DBC, built up

````md magic-move
```kotlin
@Repository
class Repo(connectionFactory: ConnectionFactory) {
}
```
```kotlin
@Repository
class Repo(connectionFactory: ConnectionFactory) {
  val client = DatabaseClient.create(connectionFactory)
}
```
```kotlin
@Repository
class Repo(connectionFactory: ConnectionFactory) {
  val client = DatabaseClient.create(connectionFactory)

  suspend fun createUserAndReturnId(): Long =
    client
}
```
```kotlin
@Repository
class Repo(connectionFactory: ConnectionFactory) {
  val client = DatabaseClient.create(connectionFactory)

  suspend fun createUserAndReturnId(): Long =
    client
      .sql("INSERT INTO users (name, email) VALUES (:n, :e) RETURNING id")
      .bind("n", "Pasha").bind("e", randomEmail())
      .fetch()
      .awaitSingle()["id"] as? Long
        ?: error("no id returned")
}
```
````

<!--
"Class shell."

[click] "Client."
[click] "A suspend function."
[click] "The chain. And awaitSingle, which is another extension function — the same leverage we just sorted, applied inside
Spring itself."

"No Mono anywhere in the signature. Nothing on this slide has changed in four
years."
-->

---
layout: two-cols-header
---

# Repositories and transactions

```kotlin {all|2|3}
interface Users : CoroutineCrudRepository<User, Long> {
  suspend fun findByEmail(email: String): User?
  fun findByLastName(name: String): Flow<User>
}
```

::left::

<v-click>

Declarative

```kotlin
@Transactional
suspend fun register(u: User) {
  // ...
}
```

</v-click>

::right::

<v-click>

Programmatic

```kotlin
// tx: TransactionalOperator
suspend fun seed() = tx.executeAndAwait {
  insertFirst()
  insertSecond()
}
```

</v-click>

<!--
"A coroutine repository."

[click] "One result: suspend."
[click] "Many results: Flow. That is the whole rule for coroutine repositories."

[click] "And @Transactional over a suspend function works. From the caller's
side, nothing about transactions changed."

[click] "For the programmatic case there is executeAndAwait on
TransactionalOperator. Everything in that block is one transaction."

Two things worth saying out loud here:

  - executeAndAwait is an EXTENSION FUNCTION. It needs
    `import org.springframework.transaction.reactive.executeAndAwait`, and your
    IDE will not suggest it. Same trap as the security DSL's `invoke` import
    later. Callback opportunity to the extensions chapter: this is the leverage
    we sorted, being used by Spring on Spring's own API.

  - For a Flow rather than a suspend fun, the equivalent is
    `flow.transactional(operator)`.
-->

---

# Then you ship it

```text {all|2}
[a1b2c3d4] Handling request for user 42
[        ] Loaded user 42 from database
```

<v-click>

<div class="mt-8 text-xl">Suspend, resume, <b>different thread</b>, empty MDC.</div>

</v-click>

<!--
"Here is the part that is genuinely new, and it is new because it only shows up
after you take that advice and run it in production for a year."

[click] "Your trace ids vanish. First line has one, second line is empty."

[click] "Micrometer propagates the current observation through a
ThreadLocal for blocking code and through the Reactor context for reactive
pipelines. A suspend function resumes on whatever thread is free, and neither
mechanism follows it."
-->

---

# The fix

```kotlin {all|1|3-5|6}
implementation("io.micrometer:context-propagation")

runBlocking(Dispatchers.IO + PropagationContextElement()) {
  waitAndLog()
}
// or globally: Hooks.enableAutomaticContextPropagation()
```

<v-click>

```text
[a1b2c3d4] Loaded user 42 from database
```

</v-click>

<!--
[click] "One dependency."

[click] "PropagationContextElement is a coroutine context element that
carries the Micrometer context across suspension points. Add it to the context."

[click] "Or turn on automatic propagation globally and stop thinking about it."

[click] "And the trace id survives the suspension."

"File this one. It is the single most common thing that makes teams give up on
coroutines in Spring, and it is four words of configuration."
-->

---

# Sorted: coroutines

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
</div>

<v-click>

<div style="color:#4ade80">
coroutines<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
</div>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> the bean DSL · the security DSL · HTTP clients · JSON · tests
</div>

<!--
[click] "Leverage — suspend and Flow are language features and no Spring release makes
them unnecessary."

(beat)

"But hold that tick loosely. Something happened to the reason we reached for
them, and we come back to it later today."
-->

---
layout: section
---

# The bean DSL

## and the glue that's gone

<!--
"This is my favourite section. In a minute you will see why that is
embarrassing."
-->

---

# One line instead of four

```kotlin
@Bean
fun kotlinMapper(): ObjectMapper {
  return jacksonObjectMapper()
}
```

<v-click>

```kotlin
val beans = beans {
  bean { jacksonObjectMapper() }
}
```

</v-click>

<!--
"Four lines and an annotation, to say: this bean is that call."

[click] "In the functional DSL it is the call."
-->

---

# It's just Kotlin in there

```kotlin {all|1-5|8|9|10-12}
class JsonLogger(private val mapper: ObjectMapper) {
  fun log(o: Any) =
    if (o::class.isData) println(mapper.writeValueAsString(o))
    else println(o.toString())
}

val beans = beans {
  bean { jacksonObjectMapper() }
  bean(::JsonLogger)
  bean("greeting", isLazyInit = Random.nextBoolean()) {
    if (Random.nextBoolean()) "USA" else "AS U"
  }
}
```

<!--
[click] "A plain class. No annotations."

[click] "The mapper bean from the previous slide."

[click] "A constructor reference is a bean definition."

[click] "And the third one is the real argument: that is an if, at
registration time, in the language. Not a SpEL string, not a @Conditional
class. An if."
-->

---

# So how do I use it?

```kotlin {all|2-4}
fun main(args: Array<String>) {
  runApplication<SampleApplication>(*args) {
    addInitializers(beans)
  }
}
```

<v-click>

```text
Started SampleApplicationKt in 1.776 seconds
```

<div class="mt-2 text-xs opacity-50">
Source: one unbenchmarked run, my laptop, 2024. The DSL's startup cost is not
measured here — if it matters to you, measure it yourself.
</div>

</v-click>

<!--
"Back to the main function."

[click] "One block on runApplication —"

[click] "— and it starts. In 2024 this is where the section got its applause."
-->

---
layout: two-cols-header
---

# Then you write a test

::left::

```kotlin
@SpringBootTest
class ConfigTest {
  @Autowired
  private lateinit var myBean: MyBean
}
```

<v-click at="2">

```text
No qualifying bean of type
'com.github.asm0dey.sample.JsonLogger'
  available: expected at least 1
  bean which qualifies as
  autowire candidate
```

</v-click>

::right::

<img src="/test-explodes.png" v-click="1" class="w-full ml-8 mt-6">

<!--
"So you write a test."

[click] (the explosion — let the picture land first)

[click] (the error text) "@SpringBootTest never calls main."

(beat — let that land on its own)

"Your beans lived in a lambda that only the main function ever passed to the
application. The test builds a context without it."
-->

---

# The 2024 fix: three artifacts

```kotlin {all|1|2-6|8-9}
val beans = beans { /* ... */ }
class BeansInitializer :
    ApplicationContextInitializer<GenericApplicationContext> {
  override fun initialize(context: GenericApplicationContext) =
    beans.initialize(context)
}

// application.yml
context.initializer.classes: "com.example.BeansInitializer"
```

<!--
[click] "A DSL,"

[click] "a class whose only job is to hand the DSL to a context,"

[click] "and a string in a yaml file that names that class. Three artifacts,
one of them stringly-typed, to make one DSL work everywhere."

"I taught this in 2024 with a straight face, because it was the answer."

Matter-of-fact. Not sheepish. The artifact is on screen; it argues
for itself.
-->

---
layout: statement
---

# Spring Framework 7 fixed this.

## Guess how.

<!--
This is the big one. Do not rush it.

"Before I show you: what would you have done?"

Genuinely take two or three answers from the room.
Do NOT signal which answer is right while collecting them.
-->

---

# `BeanRegistrarDsl`

Same three beans. Same file.

````md magic-move
```kotlin
// 2024
val beans = beans {
  bean { jacksonObjectMapper() }
  bean(::JsonLogger)
  bean("greeting", isLazyInit = Random.nextBoolean()) {
    if (Random.nextBoolean()) "USA" else "AS U"
  }
}
```
```kotlin
// 2026
class AppBeans : BeanRegistrarDsl({
  registerBean { jacksonObjectMapper() }
  registerBean { JsonLogger(bean()) }
  registerBean(name = "greeting", lazyInit = Random.nextBoolean()) {
    if (Random.nextBoolean()) "USA" else "AS U"
  }
})
```
````

<!--
"Same three beans. The mapper, the logger, the one whose
laziness is decided by a coin flip at startup."

[click] (let the morph play, then walk the three changes)

  bean { }                    →  registerBean { }
  bean(::JsonLogger)          →  registerBean { JsonLogger(bean()) }
  bean("greeting", isLazyInit)→  registerBean(name =, lazyInit =)

"bean() inside the lambda resolves a dependency — that is the same reified
trick as runApplication at the very start of the talk."

"And the named parameters are the real gain: prototype, lazyInit, description
were annotations, and are now parameters, with completion and type checking."

Also available and worth naming only if asked: profile("x") { } blocks, and an
Environment for runtime conditions.
-->

---

# And to use it

```kotlin
@Configuration
@Import(MyBeanRegistrar::class)
class MyConfiguration
```

<v-click>

<div class="mt-10 text-xl opacity-60">
<s>ApplicationContextInitializer</s> &nbsp;·&nbsp; <s>context.initializer.classes</s>
</div>

</v-click>

<!--
"Scaffolding. The hole got filled."

"@Import. That is it."

[click] "The initializer class goes. The yaml property goes. And because it is
an @Import, it is in the context the same way in main and in @SpringBootTest,
so the failure two slides ago cannot happen."
-->

---
layout: quote
---

> Bean registrars are supported with Ahead of Time Optimizations, either on the JVM or with GraalVM native images, **including when instance suppliers are used**.

<div class="text-sm opacity-50 mt-4">

https://docs.spring.io/spring-framework/reference/core/beans/java/programmatic-bean-registration.html
</div>

<!--
"Including when instance suppliers are used. If you have ever tried
to take a functional bean definition with a computed supplier into a native
image, that clause is the whole slide."
-->

---

# Sorted: the bean DSL

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
coroutines<br/>
</div>

<v-click>

<div style="color:#4ade80">
the bean DSL itself<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
</div>

<v-click>

<div style="color:#fb923c">
the DSL glue<br/>
</div>

</v-click>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> the security DSL · HTTP clients · JSON · tests
</div>

<!--
"This is the first one that splits."

[click] "The DSL is leverage — a Kotlin lambda with a receiver, which no
framework release makes unnecessary."

[click] "The initializer and the yaml property were scaffolding around a hole, and the
hole is filled. Same section of the same talk, one row in each column. That is
why the sorting question is worth asking per technique and not per chapter."
-->

---
layout: section
---

# Security

## DSLs all the way down


---

# A DSL inside a DSL

````md magic-move
```kotlin
class MyBeanRegistrar : BeanRegistrarDsl({
  registerBean<SecurityFilterChain> {
    val http = bean<HttpSecurity>()
    http.build()
  }
})
```
```kotlin
class MyBeanRegistrar : BeanRegistrarDsl({
  registerBean<SecurityFilterChain> {
    val http = bean<HttpSecurity>()
    http {
      securityMatcher("/**")
      csrf { disable() }
      httpBasic { }
      authorizeHttpRequests {
        authorize("/auth/**", authenticated)
        authorize(anyRequest, permitAll)
      }
    }
    http.build()
  }
})
```
````

<!--
"A SecurityFilterChain bean, registered in the bean DSL."

[click] "The Security DSL nests inside the bean registration DSL, because
both are the same Kotlin feature: a lambda with a receiver."

"This is why the bean DSL was worth Spring fixing. Not because @Bean methods
are verbose. Because once registration is a DSL, your whole configuration
surface composes into one readable block instead of being scattered across
annotated classes."
-->

---

# Two things that bite

```kotlin
import org.springframework.security.config.annotation.web.invoke
```

<div class="text-sm opacity-60">without it, <code>http { }</code> doesn't resolve — and no IDE will suggest it</div>

<v-click>

<div class="mt-10 text-2xl">
<s>authorizeRequests</s> &nbsp;→&nbsp; <b>authorizeHttpRequests</b>
</div>

</v-click>

<!--
"The invoke operator that makes http-brace-brace legal is an
extension, and it is not imported for you. Every person who has tried this has
lost ten minutes to it, because there is nothing to auto-complete."

[click] "And authorizeRequests is gone. It is authorizeHttpRequests now. If you
learned this from anything written before 2024, yours says the old one. So does
mine. That is scaffolding of a third kind — the kind where the API just moved
underneath everybody at once."
-->

---

# Sorted: security

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
coroutines<br/>
the bean DSL itself<br/>
the security DSL<br/>
</div>

<v-click>

<div style="color:#4ade80">
the security DSL<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
the DSL glue<br/>
authorizeRequests<br/>
</div>

<v-click>

<div style="color:#fb923c">
authorizeRequests<br/>
</div>

</v-click>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> HTTP clients · JSON · tests
</div>

<!--
Ask before either click lands.

"Leverage or scaffolding?"

[click] "The DSL itself is leverage — it is a lambda with a receiver, and it
nests inside the bean registrar. No Spring release makes that unnecessary."

[click] "authorizeRequests is the third kind: not a hole that got filled, just
an API that moved underneath everybody at once. Scaffolding all the same —
it came due."
-->

---
layout: section
class: text-center
---

# Coroutines or virtual threads?

## What's actually new

---

# The premise changed

<div class="text-xl opacity-70">
2024: "Use coroutines, because the alternative is blocking a platform thread."
</div>

<v-click>

<div class="mt-12 text-3xl">
2026: a blocking call <b>unmounts</b> the virtual thread.
</div>

<div class="mt-4 text-xl opacity-70">
The carrier thread is released and runs someone else.
</div>

</v-click>

<!--
"Notice what my 2024 argument actually rested on. Not that
coroutines are elegant — that the alternative was unacceptable."

[click] "Loom removed the alternative's cost, so the argument has to be re-made rather than repeated."
-->

---

# One property

```yaml
spring.threads.virtual.enabled: true
```

<v-clicks>

- the web server's request handling
- the `@Async` executor
- listener container threads

</v-clicks>

<v-click>

<div class="mt-8 text-sm opacity-60">
Requires Java 21+ &nbsp;·&nbsp; but see the next slide for why you want 24+
</div>

</v-click>

<!--
"One property, available since Boot 3.2 and unchanged in 4. It is
not a niche switch — it rewires"

[click] "how requests are handled,"
[click] "which executor runs your @Async methods,"
[click] "and how your Kafka listeners get threads."

[click] "Java 21 is enough to turn it on. The next slide is why you want 24."
-->

---
layout: two-cols-header
---

# The boring version scales now

::left::

```kotlin
suspend fun findUser(id: Long): User =
  client.sql("...").awaitSingle()
```

<div class="mt-3 text-sm opacity-60">non-blocking, coloured</div>

::right::

```kotlin
fun findUser(id: Long): User =
  jdbcTemplate.queryForObject("...")
```

<div class="mt-3 text-sm opacity-60">blocking, uncoloured,<br/>on a virtual thread</div>

<!--
"Right-hand side: no suspend, no Flow, no R2DBC driver, no coloured functions,
and under I/O-bound load it will hold up. That was not true in 2024, and it is
true now."

(hand straight on to the next slide — the caveats live there, not here)
-->

---

# What virtual threads don't give you

<v-clicks>

- ✕ nothing for CPU-bound work
- ✕ no back-pressure
- ✕ no *stable* structured concurrency \*
- ✕ **pinning**: a native frame still pins the carrier
- ✕ your connection pool is still the ceiling

</v-clicks>

<v-click>

<div class="mt-6 text-sm opacity-60">

\* preview since JDK 21 — sixth preview in 26 (JEP 525), seventh proposed for 27.<br/>
On JDK 21–23 <code>synchronized</code> pinned too, which is why JDBC drivers,
HikariCP and Caffeine moved to <code>ReentrantLock</code>. JEP 491 fixed that in JDK 24.
</div>

</v-click>

<!--
Deliberately unhurried. Do not rush this list.

"Virtual threads make blocking cheap."

[click] "They do not make computation faster,"
[click] "they do not give you a way to say slow down,"
[click] "and they do not give you a scope that cancels its children when one
fails — not a stable one."
[click] "A native frame still pins the carrier thread."
[click] "And the honest one: if your pool is fifty connections, ten thousand
virtual threads queue on fifty connections. Loom moved the bottleneck. It did
not delete it."

[click] (footnote — only if someone asks) structured concurrency is still a
preview in Java; on JDK 21–23 synchronized pinned too, JEP 491 fixed it in 24.
-->

---

# Which one, then?

<div class="grid grid-cols-2 gap-10 mt-2">
<div>

Java — still preview, five years on

```java
try (var s = StructuredTaskScope.open()) {
  var u = s.fork(() -> findUser(id));
  var o = s.fork(() -> fetchOrder(id));
  s.join();
  return new Response(u.get(), o.get());
}
```

</div>
<div>

Kotlin — stable since 2018

```kotlin
coroutineScope {
  val user  = async { findUser(id) }
  val order = async { fetchOrder(id) }
  Response(user.await(), order.await())
}
```

</div>
</div>

<v-click>

<div class="mt-6 text-xl">
Virtual threads by default. Coroutines for <b>structured concurrency,
cancellation or back-pressure</b> — not for "non-blocking".
</div>

</v-click>

<style>
.slidev-layout pre, .slidev-layout code { font-size: 0.8em; }
</style>

<!--
"Two ways to fetch two things at once. One of them has been stable since 2018."

[click] This is the sentence people will quote in the hallway. Deliver it
slowly and do NOT qualify it afterwards.

"They are not competitors. Coroutines can run on virtual threads. But if you
are choosing, choose on the feature you actually need, not on the word
non-blocking."
-->

---
layout: section
---

# HTTP clients

## Declared, not written


---

# Every service has this file

```kotlin
suspend fun getRepo(owner: String, repo: String): Repository =
  webClient.get()
    .uri("/repos/{owner}/{repo}", owner, repo)
    .accept(MediaType.APPLICATION_JSON)
    .awaitExchange()
    .awaitBody()
```

<!--
"Every service you have ever worked on has a file of these, and they
are all the same five calls in the same order around a different path."
-->

---

# Declare it instead

```kotlin {all|1-2|4-8|5|11-12}
@HttpExchange(url = "https://api.github.com")
interface GitHubService {

  @GetExchange("/repos/{owner}/{repo}")
  suspend fun getRepo(
    @PathVariable owner: String,
    @PathVariable repo: String,
  ): Repository

  @GetExchange("/search/repositories")
  fun search(@RequestParam q: String,
             @RequestParam sort: String?): Flow<Repository>
}
```

<v-click>

<div class="mt-3 text-sm">
<span class="opacity-50"><code>@HttpExchange</code> / <code>@GetExchange</code> — Spring Framework. Java gets exactly this.</span><br/>
<span style="color:#4ade80"><code>suspend</code> · <code>String?</code> · <code>Flow&lt;T&gt;</code> — the Kotlin part.</span>
</div>

</v-click>

<!--
"An interface. No implementation, and you never write one."

[click] "The base URL lives on the interface."

[click] "One endpoint, one method."

[click] "suspend on an interface method. The proxy handles it."

[click] (both lines lit, which is the point)
"q is not nullable, so it is required. sort is nullable, so it is optional. The nullability of the type carries the decision that in Java needs
a required=false attribute."

[click] (the legend) Say this out loud, do not let them assume otherwise:
"The interface and the annotations are Spring, not Kotlin. Java gets exactly
this. What Kotlin adds is the three things in green — suspend on an interface
method, nullability standing in for required=false, and Flow instead of Flux."
-->

---

# Boot 4 wires it

```kotlin {all|1|4}
import org.springframework.web.service.registry.ImportHttpServices

@SpringBootApplication
@ImportHttpServices(basePackages = ["com.example.myclients"])
class MyApplication

fun main(args: Array<String>) {
  runApplication<MyApplication>(*args)
}
```

<v-click>

<div class="mt-6 text-lg">
An interface is not a bean. <b>Something has to build the proxy and register
it</b> — and until Boot 4 that something was you.
</div>

</v-click>

<!--
"Here is the thing nobody tells you: that interface is not a bean. It has no
implementation. Somebody has to generate a proxy for it and put that proxy in
the context."

"Until Boot 4 that somebody was you: build a RestClient, wrap it in a
RestClientAdapter, feed that to HttpServiceProxyFactory.builderFor(...), call
createClient for every single interface, and register each result as a bean.
Roughly ten lines per client, and every project wrote them."

[click] (line 1 — the import) "One annotation, from a package you have not
seen before."

[click] (line 4) "@ImportHttpServices scans the package and does all of it. That is why the
annotation exists, and the ten lines it replaces were textbook scaffolding."

[click] "And note where we are: back in the main function, for the third time
today. Same file, three different lessons. An interface is not a bean — and
until Boot 4, the thing that made it one was you."
-->

---

# Sorted: HTTP clients

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
coroutines<br/>
the bean DSL itself<br/>
the security DSL<br/>
</div>

<v-click>

<div style="color:#4ade80">
suspend + nullability<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
the DSL glue<br/>
authorizeRequests<br/>
</div>

<v-click>

<div style="color:#fb923c">
proxy-factory wiring<br/>
</div>

</v-click>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> JSON · tests
</div>

<!--
Ask, then WAIT.

"Leverage or scaffolding?"

"Both."

[click] "The suspend and the nullable parameter are language, and they are why
the Kotlin version reads better than the Java one."

[click] "The proxy wiring is framework, and Boot does it now."
-->

---
layout: section
---

# JSON

## kotlinx.serialization, wired for you

---

# 2024: you wire the converter

```kotlin {all|2|6}
@Configuration
class JsonConfig : WebMvcConfigurer {
  override fun extendMessageConverters(
    converters: MutableList<HttpMessageConverter<*>>,
  ) {
    converters.add(0, KotlinSerializationJsonHttpMessageConverter())
  }
}
```

<v-click>

<div class="mt-6 text-lg">
Spring ships the converter — and doesn't register it, because it <b>conflicts with Jackson</b>.
</div>

</v-click>

<!--
"If you wanted kotlinx.serialization instead of Jackson in 2024, this was
your file."

[click] "A WebMvcConfigurer, just to get at the converter list."

[click] "And index zero. Not add — add at zero. Put it anywhere else and
Jackson answers first, and you spend an afternoon wondering why your
annotations do nothing."

[click] "Spring has had this converter for years. It just will not turn it
on for you, because it fights with Jackson for the same content type."
-->

---

# Boot 4: one dependency

```kotlin
implementation("org.springframework.boot:spring-boot-starter-kotlinx-serialization-json")
```

<v-click>

```yaml
spring.kotlinx.serialization.json:
  ignore-unknown-keys: true
  naming-strategy: snake-case
```

</v-click>

<v-click>

<div class="mt-6 text-lg">
A <code>Json</code> bean from properties. The converter goes <b>ahead of Jackson</b> — Jackson stays as the fallback.
</div>

</v-click>

<!--
"Boot 4 has a starter for it."

[click] "The Json instance is a bean now, and its knobs are properties. The
same knobs you used to set in a Json { } builder in that config class."

[click] "And the converter is registered for you, in front of Jackson. The
config class from the previous slide is gone."
-->

---

# The part that was never glue

```kotlin {all|2|5}
plugins {
  kotlin("plugin.serialization") version "2.4.21"
}

@Serializable
data class Person(val id: Int, val name: String, val age: Double)
```

<v-click>

<div class="mt-6 text-lg">
The serializer is <b>generated by the compiler</b>. No reflection at runtime.
</div>

</v-click>

<!--
"Now the part that did not change, because it never needed Spring."

[click] "A compiler plugin."

[click] "One annotation. The compiler writes the serializer for this class at
build time."

[click] "So it does not inspect your class at runtime. A Kotlin compiler
feature that no Spring release will take away."
-->

---

# Sorted: JSON

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
coroutines<br/>
the bean DSL itself<br/>
the security DSL<br/>
suspend + nullability<br/>
</div>

<v-click>

<div style="color:#4ade80">
@Serializable<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
the DSL glue<br/>
authorizeRequests<br/>
proxy-factory wiring<br/>
</div>

<v-click>

<div style="color:#fb923c">
converter registration<br/>
</div>

</v-click>

</div>
</div>

<div class="mt-5 pt-3 border-t border-gray-700 text-sm">
<span class="opacity-60">still to sort:</span> tests
</div>

<!--
"Leverage or scaffolding?"

[click] "The annotation and the compiler plugin: leverage."

[click] "The config class that put the converter at index zero: scaffolding.
Boot does it now."
-->

---
layout: section
---

# Tests

## Where the most scaffolding was hiding

---

# The 2024 test

```kotlin {all|2|4-5|8|9}
@SpringBootTest
@AutoConfigureMockMvc
class PersonApiTest {
  @Autowired lateinit var mockMvc: MockMvc
  @MockBean lateinit var people: PersonRepo

  @Test
  fun `finds a person`() = runBlocking {
    whenever(people.findById(1)).thenReturn(Person(1, "Ann", 33.0))
    mockMvc.get("/people/1").andExpect { status { isOk() } }
  }
}
```

<!--
"Here is a test for the service we built. Before I say anything: how many
of you have a lateinit var in your test sources right now?"

(count it, briefly)

[click] "MockMvc, because that was how you talked HTTP to the app."

[click] "Two lateinit vars. Kotlin wants values in the constructor, and the
test runner wanted a no-arg class. Lateinit was the compromise."

[click] "runBlocking, because the repository is suspend and the test was not
allowed to be."

[click] "And whenever, from mockito-kotlin, because Mockito does not know what
a suspend function is."
-->

---

# It doesn't compile anymore

<div class="text-2xl mt-6">

<code class="line-through opacity-50">@MockBean</code> &nbsp; <code class="line-through opacity-50">@SpyBean</code> — <b>removed</b> in Boot 4

</div>

<v-click>

<div class="text-xl mt-8">

<code>@MockitoBean</code> · <code>@MockitoSpyBean</code> — in Spring Framework itself

</div>

</v-click>

<v-click>

<div class="text-xl mt-4" style="color:#4ade80">

<code>@MockkBean</code> · <code>@MockkSpyBean</code> — SpringMockK 5, built on the same mechanism

</div>

</v-click>

<!--
"Deprecated in 3.4, removed in Boot 4. This test does not compile anymore."

[click] "The replacement lives in Spring Framework, not Boot."

[click] "And for Kotlin: SpringMockK 5 is a rewrite on top of that same
mechanism. Same annotation you know, MockkBean. SpykBean is now MockkSpyBean."
-->

---

# `suspend`, natively

```kotlin {all|2|3}
@Test
suspend fun `finds a person`() {
  coEvery { people.findById(1) } returns Person(1, "Ann", 33.0)
  // ...
}
```

<v-click>

<div class="mt-6 text-lg">
Spring Framework 7 requires <b>JUnit 6</b>. JUnit 6 runs <code>suspend</code> tests itself.
</div>

</v-click>

<!--
"Spring Framework 7 moved its baseline to JUnit 6, and JUnit 6 knows what a
suspend function is."

[click] "suspend, on the test. No runBlocking."

[click] "coEvery from MockK, which always knew about suspend."

[click] "One caveat, because someone will hit it: under the hood JUnit still
uses runBlocking. If you need virtual time, to skip a delay, you still want
runTest."
-->

---

# Constructor, not `lateinit`

```kotlin
class PersonApiTest(@Autowired val client: RestTestClient)
```

<v-click>

```properties
# junit-platform.properties — once, for every test class
spring.test.constructor.autowire.mode=all
```

</v-click>

<!--
"The lateinit was never necessary. Spring can inject the test's
constructor."

[click] "And if you do not want the annotation on every parameter, one line
in junit-platform.properties turns it on for the whole test suite."
-->

---

# `RestTestClient`

```kotlin {all|1|4}
client.get().uri("/people/1")
  .exchange()
  .expectStatus().isOk()
  .expectBody<Person>()
```

<v-click>

<div class="mt-6 text-lg">
New in Framework 7. <code>expectBody&lt;Person&gt;()</code> is a <b>reified extension</b> — the same trick as <code>JdbcTemplate.query</code>.
</div>

</v-click>

<!--
"Framework 7 has a new test client: RestTestClient. Same fluent style as
WebTestClient, but for the blocking stack, and it can sit on MockMvc or on a
real port."

[click] "Same calls you know."

[click] "And this is the bit that should look familiar. expectBody of Person,
with angle brackets, no class literal."

[click] "A reified extension function. The same leverage as the JdbcTemplate
one we wrote at the start, shipped by Spring this time."
-->

---

# The 2026 test

```kotlin {all|2|3|5|7-8}
@SpringBootTest
@AutoConfigureRestTestClient
class PersonApiTest(@Autowired val client: RestTestClient) {

  @MockkBean lateinit var people: PersonRepo

  @Test
  suspend fun `finds a person`() {
    coEvery { people.findById(1) } returns Person(1, "Ann", 33.0)
    client.get().uri("/people/1").exchange()
      .expectStatus().isOk()
      .expectBody<Person>()
  }
}
```

<!--
"All of it together."

[click] "Boot 4 does not hand you a test client on @SpringBootTest anymore —
you ask for the one you want."

[click] "The client comes through the constructor."

[click] "One lateinit left, for the mock. Honest count: one, down from two."

[click] "And the test is suspend. No runBlocking anywhere."
-->

---

# Sorted: tests

<div class="grid grid-cols-2 gap-10 mt-4">
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#4ade80;opacity:1">LEVERAGE</div>
<div class="text-sm opacity-50 mb-4">still pays in 2030</div>

<div class="opacity-40">
extension functions<br/>
vararg reordering<br/>
coroutines<br/>
the bean DSL itself<br/>
the security DSL<br/>
suspend + nullability<br/>
@Serializable<br/>
</div>

<v-click>

<div style="color:#4ade80">
reified expectBody&lt;T&gt;()<br/>
</div>

</v-click>

</div>
<div>

<div class="text-2xl font-bold tracking-wider" style="color:#fb923c;opacity:1">SCAFFOLDING</div>
<div class="text-sm opacity-50 mb-4">came due</div>

<div class="opacity-40">
-Xjsr305=strict<br/>
the DSL glue<br/>
authorizeRequests<br/>
proxy-factory wiring<br/>
converter registration<br/>
</div>

<v-click>

<div style="color:#fb923c">
runBlocking in tests<br/>
@MockBean<br/>
</div>

</v-click>

</div>
</div>

<!--
"Last one. Leverage or scaffolding?"

[click] "The reified extension: leverage. It was leverage in 2024 in our own
code, and it is leverage now that Spring ships it."

[click] "runBlocking and MockBean: scaffolding. Both gone, one by JUnit, one
by Boot."
-->

---
layout: image
image: /slide-2024-jsr305-struck.png
---

<!--
"Fifty minutes ago I asked who had this in a build file, [M] of you said you
write Spring in Kotlin, and [N] of you had this flag." — the two numbers you counted at the start.

-->

---

# What leaves your codebase

```diff
- freeCompilerArgs = listOf("-Xjsr305=strict")
- kotlinOptions { }
- class BeansInitializer : ApplicationContextInitializer<…>
- context.initializer.classes: "…"
- HttpServiceProxyFactory.builderFor(adapter).build()
- class UserRowMapper implements RowMapper<Person>
- .authorizeRequests { }
- converters.add(0, KotlinSerializationJsonHttpMessageConverter())
- @MockBean lateinit var people: PersonRepo
- fun `finds a person`() = runBlocking {
```

<!--
"Your recap is a diff, not a to-do list. Ten deletions. That is what your
Kotlin-and-Spring codebase looks like on Monday if you act on this talk:
smaller, in ten specific places."

(let them photograph it — PAUSE and stop talking)

-->

---
layout: statement
---

# Language <span style="color:#4ade80">leverage</span> compounds.

<v-click>

<div class="mt-12 text-3xl opacity-90">
Framework <span style="color:#fb923c">scaffolding</span> is a loan
against a future release.
</div>

</v-click>

<!--
[click] "Everything else I said today has a shelf life. I know that, because half of
this talk was me correcting the other half."

"This does not. Ask it of any Kotlin trick you use with Spring: could a
framework release make this unnecessary? If yes, you are holding scaffolding —
fine, use it, and know it comes due. If no, it compounds."

-->

---

# Three things

<v-clicks>

| when | what |
|---|---|
| **Today** | delete `-Xjsr305=strict`, fix `kotlinOptions` |
| **This month** | replace the initializer glue with `@Import` |
| **Ongoing** | sort before you adopt |

</v-clicks>

<!--
"One thing today,"
[click] (Today row) "delete the flag, fix kotlinOptions."
[click] (This month row) "replace the initializer glue with @Import."
[click] (Ongoing row) "and one habit: sort before you adopt. The first two are
find-and-replace. The third is the one that matters."
-->

---

# Everything is in the repo

<div class="text-lg">

[github.com/asm0dey/**kotlin-spring-dev2next**](https://github.com/asm0dey/kotlin-spring-dev2next) — these slides and every sample in them

docs.spring.io/spring-framework/reference/languages/kotlin/

</div>

<div class="mt-10 text-xl">
<a href="https://asm0dey.site" target="_blank">asm0dey.site</a> — all my contacts
</div>

<div class="mt-10 text-sm opacity-60">
I work on Liberica JDK at BellSoft — the JDK spring.io/quickstart recommends.
If today was useful and you want more of it, that's where it comes from.
</div>

<!--
"Every slide and every sample is in that repo, so you do not have to
trust a screenshot."

(then STOP — hand over to questions without a subscription pitch)

"Questions."
-->

---
layout: end
---
