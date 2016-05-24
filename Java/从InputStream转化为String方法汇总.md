### 1.使用 **IOUtils.toString** (Apache Common Utils)

```java
String result = IOUtils.toString(inputStream, StandardCharsets.UTF_8);
```

### 2.使用**CharStreams** (guava)

```Java
String result = CharStreams.toString(new InputStreamReader(inputStream, Charsets.UTF_8));
```

### 3.使用**Scanner** (JDK)

```java
Scanner s = new Scanner(inputStream).useDelimiter("\\A");
String result = s.hasNext() ? s.next() : "";
```

### 4.使用**Stream Api** (Java 8).  
**Warning**: This solution convert different linebreaks (like ```\r\n```) to ```\n```.

```java
String result = new BufferedReader(new InputStreamReader(inputStream)).lines().
                collect(Collectors.joining("\n"));
```

### 5.使用 **parallel Stream Api** (Java 8). 

**Warning**: This solution convert different linebreaks (like ```\r\n```) to ```\n```.  

```java
String result = new BufferedReader(new InputStreamReader(inputStream)).lines()
   .parallel().collect(Collectors.joining("\n"));
```

### 6.使用**InputStreamReader and StringBuilder** (JDK)

```java
final int bufferSize = 1024;
final char[] buffer = new char[bufferSize];
final StringBuilder out = new StringBuilder();
Reader in = new InputStreamReader(inputStream, "UTF-8");
for (; ; ) {
    int rsz = in.read(buffer, 0, buffer.length);
    if (rsz < 0)
        break;
    out.append(buffer, 0, rsz);
}
return out.toString();
```

### 7.使用**StringWriter and IOUtils.copy** (Apache Commons)

```java
StringWriter writer = new StringWriter();
IOUtils.copy(inputStream, writer, "UTF-8");
return writer.toString();
```

### 8.使用**ByteArrayOutputStream**和**inputStream.read** (JDK)

```java
ByteArrayOutputStream result = new ByteArrayOutputStream();
byte[] buffer = new byte[1024];
int length;
while ((length = inputStream.read(buffer)) != -1) {
    result.write(buffer, 0, length);
}
return result.toString("UTF-8");
```

### 9.使用**BufferedReader** (JDK). 

**Warning**: This solution convert different linebreaks (like ```\n\r```) to line.separator system property (for example, in Windows to "```\r\n```").

```java
String newLine = System.getProperty("line.separator");
BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
StringBuilder result = new StringBuilder();
String line; boolean flag = false;
while ((line = reader.readLine()) != null) {
    result.append(flag? newLine: "").append(line);
    flag = true;
}
return result.toString();
```

### 10. 使用**BufferedInputStream** 和 **ByteArrayOutputStream** (JDK)

```java
BufferedInputStream bis = new BufferedInputStream(inputStream);
ByteArrayOutputStream buf = new ByteArrayOutputStream();
int result = bis.read();
while(result != -1) {
    buf.write((byte) result);
    result = bis.read();
}
return buf.toString();
```

### 11.使用**inputStream.read()** 和 **StringBuilder** (JDK). 

**Warning**: This soulition has problem with Unicode, for example with Russian text (work correctly only with non-Unicode text)

```java
int ch;
StringBuilder sb = new StringBuilder();
while((ch = inputStream.read()) != -1)
    sb.append((char)ch);
reset();
return sb.toString();
```

**Warning**:

1. Solutions 4, 5 and 9 convert different linebreaks to one.
2. Soulution 11 can't work correclty with Unicode text
