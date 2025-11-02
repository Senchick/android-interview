## Kotlin Coroutines, Flow

### Оглавление

* [Корутины](#корутины)
* [Flow](#flow)
* [Практика и тонкости](#практика-и-тонкости)

### Корутины

- #### Корутины
  **Корутины** — это легковесные потоки, которые упрощают асинхронное программирование, позволяя писать асинхронный код последовательно. Они помогают избежать коллбэков и сложной цепочки промисов, делая код чище и понятнее.

- #### `suspend`
  Ключевое слово **suspend** используется для маркировки функций, которые могут приостанавливать выполнение корутины без блокировки потока. Такие функции могут быть вызваны только из другой suspend функции или из корутины.

- #### `launch`, `async-await`, `withContext`
    - **launch**: Запускает новую корутину без блокировки текущего потока и возвращает ссылку на Job. Не предоставляет прямой доступ к результату выполнения.
    - **async** и **await**: async используется для запуска корутины, которая возвращает Deferred — обещание результата. await используется для ожидания этого результата без блокировки потока.
    - **withContext**: Позволяет изменить контекст выполнения корутины, например, для переключения между потоками, и возвращает результат выполнения блока кода.

- #### Dispatchers
    - **Dispatchers.Main** <br/>Используется для выполнения корутин в главном потоке пользовательского интерфейса (UI). Это критически важно для Android разработки, поскольку изменения UI должны производиться в главном потоке.
    - **Dispatchers.IO** <br/>Предназначен для выполнения операций ввода-вывода, таких как сетевые запросы, чтение и запись файлов, операции с базами данных и т.п.
    - **Dispatchers.Default** <br/> Это диспетчер по умолчанию, оптимизированный для выполнения вычислительно-интенсивных задач в общем пуле фоновых потоков.
    - **Dispatchers.Unconfined** <br/> Этот диспетчер не привязан к конкретному потоку. Он начинает выполнение корутины в текущем потоке, но после первой приостановки, продолжение будет выполнено в потоке, который первым вызвал его.


- #### `coroutineScope`, `coroutineContext`, `job`, `interface CoroutineScope`, `fun CoroutineScope`
    - **coroutineScope**. Это функция-билдер, которая создает новую область видимости корутины. Все корутины, запущенные внутри этой области, должны быть завершены, прежде чем coroutineScope вернет управление вызывающей стороне. Это полезно для структурирования кода и управления жизненным циклом группы корутин.
    - **coroutineContext**. Это свойство, доступное внутри корутины, которое содержит контекст выполнения корутины, включая Job и Dispatcher. Контекст корутины управляет поведением корутины, например, в каком потоке она будет выполняться и как должны быть обработаны исключения.
    - **Job**. Это ключевой компонент в системе корутин Kotlin, который представляет собой отдельную задачу, выполнение которой может быть отменено. Job позволяет управлять жизненным циклом корутины, включая ее отмену и завершение.
    - **interface CoroutineScope**.
    - **fun CoroutineScope**.

- #### `lifecycleScope`, `viewModelScope`, `GlobalScope`
    - **lifecycleScope**: Привязан к жизненному циклу **Activity** или **Fragment** и автоматически отменяет корутину, когда компонент уничтожается.
    - **viewModelScope**: Привязан к жизненному циклу **ViewModel** и отменяет корутины при очистке **ViewModel**.
    - **GlobalScope**: Глобальный скоуп корутины, который не привязан к жизненному циклу и должен использоваться **с осторожностью**, так как может привести к утечкам памяти.

- #### `suspendCoroutine`, `suspendCancellableCoroutine`
    - **suspendCoroutine**: Это низкоуровневая функция, которая приостанавливает текущую корутину до тех пор, пока не будет вызван один из переданных в нее колбэков. Она позволяет интегрировать корутины с асинхронными колбэк-базированными API.
    - **suspendCancellableCoroutine**: Расширяет suspendCoroutine добавлением поддержки отмены. Если корутина, ожидающая в suspendCancellableCoroutine, отменяется, то можно обработать эту отмену и корректно завершить работу, например, освободить ресурсы.

- #### `coroutineScope`, `supervisorScope`
    - **coroutineScope**: Если одна из дочерних корутин внутри coroutineScope завершается с исключением, coroutineScope отменяет все остальные дочерние корутины и пропагирует исключение дальше.
    - **supervisorScope**: В отличие от coroutineScope, supervisorScope позволяет дочерним корутинам завершаться независимо. Если одна дочерняя корутина завершается с исключением, supervisorScope не отменяет остальные дочерние корутины. Это полезно в ситуациях, когда необходимо обеспечить независимое выполнение дочерних корутин внутри одной области видимости.

- #### `run`, `runCatching`,`runBlocking`,`runInterruptible`
    - **run**.
    - **runCatching**.
    - **runBlocking**.
    - **runInterruptible**.

### Flow

- #### Что такое Flow?
  `Flow` в Kotlin – это тип, который может асинхронно предоставлять значения. Он поддерживает асинхронные потоки данных и используется для представления значений, которые могут быть доступны в будущем. `Flow` позволяет управлять асинхронным кодом более удобно и функционально.
- #### Flow vs Coroutines
  В сопрограммах поток — это тип, который может выдавать несколько значений последовательно, в отличие от suspend функций, которые возвращают только одно значение

- #### Flow Builder, Operator, Collector
    - `Flow` создаётся с помощью `builder` функций. Самый базовый builder – это `flow {}`, внутри которого вы можете отправлять значения с помощью `emit()`.
    - `Операторы Flow` позволяют трансформировать, фильтровать, комбинировать и выполнять другие операции с потоками данных. Например, `map` и `filter`.
    - `Collector` – это терминальная операция, которая запускает выполнение flow и обрабатывает каждое значение, отправленное в поток. В примерах выше использовался collect {} как коллектор.
- #### `flowOn`, Dispatchers
  **flowOn** позволяет изменить контекст выполнения операций внутри потока (Flow). Это особенно полезно, когда тяжелые операции должны выполняться в фоновом потоке, а результаты обрабатываться в основном потоке
  ```kotlin
  import kotlinx.coroutines.*
  import kotlinx.coroutines.flow.*

  fun main() = runBlocking {
    flow {
        for (i in 1..3) {
            Thread.sleep(100) // Имитация длительной операции
            emit(i)
        }
    }.flowOn(Dispatchers.Default) // Выполнение в фоновом потоке
    .map { value ->
        "Преобразованное значение $value"
    }
    .collect { value ->
        println("$value на потоке ${Thread.currentThread().name}")
    }
  }
  ```

- #### Операторы, такие как `filter`, `map`, `zip`, `flatMapConcat`, `retry`, `debounce`, `distinctUntilChanged`, `flatMapLatest`
    - **filter**: Отфильтровывает элементы, не соответствующие условию.
    - **map**: Преобразует элементы в другие объекты.
    - **zip**: Комбинирует два потока данных, сопоставляя их элементы.
    - **flatMapConcat**: Преобразует каждый элемент в поток и объединяет эти потоки последовательно.
    - **retry**: Повторяет поток при возникновении ошибки.
    - **debounce**: Эмитирует элементы с задержкой, игнорируя быстро последующие элементы.
    - **distinctUntilChanged**: Пропускает элементы, значение которых отличается от предыдущего.
    - **flatMapLatest**: Аналогично flatMapConcat, но при появлении нового элемента отменяет предыдущий преобразованный поток.

- #### Терминальные операторы
  **Терминальные операторы** - это те, которые запускают выполнение потока и обычно возвращают результат или вызывают сайд-эффекты (например, collect, toList, toSet, first, reduce).
- #### Cold Flow против Hot Flow
    - **Cold Flow**: Не начинает выполнение до вызова терминального оператора, обеспечивая ленивость и удобство создания потоков данных.
    - **Hot Flow**: Активен независимо от наличия подписчиков, подходит для представления данных, которые изменяются во времени (например, пользовательский ввод).

- #### `StateFlow`, `SharedFlow`, `callbackFlow`, `channelFlow`
    - **StateFlow**: Хранит текущее состояние и извещает подписчиков о его изменении. Это Hot Flow. Требуется начальное значение, и он выдает его, как только коллектор начинает собирать данные. Он не выдает последовательные повторяющиеся значения. Он выдает значение только в том случае, если оно отличается от предыдущего элемента.
    - **SharedFlow**: Более общий Hot Flow, который может репрезентировать множество значений и имеет более гибкие настройки. Не требует начального значения, поэтому по умолчанию не выдает никаких значений. Он выдает все значения и не заботится об отличиях от предыдущего элемента. Он также выдает последовательные повторяющиеся значения.
    - **callbackFlow** и **channelFlow**: Позволяют создавать потоки на основе коллбэков или событий, удобно применять для интеграции с API, основанными на коллбэках.

- #### `StateIn`, `SharedIn`
  Эти операторы используются для преобразования Cold Flow в Hot Flow (StateFlow или SharedFlow соответственно), делая поток активным и позволяя сохранять текущее состояние или делиться им сразу с несколькими подписчиками.

### Практика и тонкости

- #### Как бы Вы реализовали перевод синхронного кода в асинхронный с помощью Coroutines?
  - **callbackFlow**
```kotlin
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow
import okhttp3.*
import okio.ByteString

fun OkHttpClient.webSocketFlow(url: String): Flow<String> = 
    callbackFlow {
        val req = Request.Builder().url(url).build()
        val listener = object : WebSocketListener() {
            override fun onOpen(ws: WebSocket, response: Response) { /* optional */ }
            override fun onMessage(ws: WebSocket, text: String) { trySend(text).isSuccess }
            override fun onMessage(ws: WebSocket, bytes: ByteString) { trySend(bytes.utf8()).isSuccess }
            override fun onClosing(ws: WebSocket, code: Int, reason: String) { ws.close(code, reason); close() }
            override fun onFailure(ws: WebSocket, t: Throwable, r: Response?) { close(t) }
        }
        val ws = newWebSocket(req, listener)
    
        awaitClose { ws.cancel() } // или ws.close(NORMAL_CLOSURE, null)
    }
```
  - **suspendCancellableCoroutine**
```kotlin
import kotlinx.coroutines.suspendCancellableCoroutine
import okhttp3.*
import java.io.IOException
import kotlin.coroutines.resume
import kotlin.coroutines.resumeWithException

suspend fun OkHttpClient.getBody(url: String): String =
    suspendCancellableCoroutine { cont ->
    val req = Request.Builder().url(url).build()
    val call = newCall(req)

        cont.invokeOnCancellation { call.cancel() }
    
        call.enqueue(object : Callback {
            override fun onFailure(call: Call, e: IOException) {
                if (!cont.isCancelled) cont.resumeWithException(e)
            }
            override fun onResponse(call: Call, response: Response) {
                response.use {
                    if (!it.isSuccessful) {
                        cont.resumeWithException(IOException("HTTP ${it.code}"))
                    } else {
                        cont.resume(it.body?.string().orEmpty())
                    }
                }
            }
        })
}
```