# icu4j
* https://unicode-org.github.io/icu/userguide/icu4j/
* https://mvnrepository.com/artifact/com.ibm.icu/icu4j
* пример использования с интеграцией Spring Boot messages и кэшированием и подключением icu4j к дефолтному резолверу messages в Spring (`HierarchicalMessageSource`) https://github.com/yakworks/spring-icu4j

# пример кастомизации для чисел
```java
import com.ibm.icu.text.RuleBasedNumberFormat;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ClassPathResource;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import java.io.IOException;
import java.nio.file.Files;
import java.util.Locale;

@Service
public class RuleBasedNumberService {

    private RuleBasedNumberFormat uzbekFormatter;

    @PostConstruct
    public void loadRules() throws IOException {
        // Load the rule file using Spring's Resource class
        Resource resource = new ClassPathResource("uzbek_rules.txt");
        String rules = Files.readString(resource.getFile().toPath());
        uzbekFormatter = new RuleBasedNumberFormat(rules, Locale.forLanguageTag("uz"));
    }

    public String formatUzbekNumber(double number) {
        return uzbekFormatter.format(number);
    }
}

```

# пример для денег

```java
import com.ibm.icu.text.NumberFormat;
import com.ibm.icu.util.Currency;
import com.ibm.icu.util.ULocale;

public class CurrencyExample {
    public static void main(String[] args) {
        // Define the locale for Uzbek
        ULocale uzbekLocale = new ULocale("uz");

        // Get currency formatter for the locale
        NumberFormat currencyFormat = NumberFormat.getCurrencyInstance(uzbekLocale);

        // Set the currency to Uzbekistani Som (UZS)
        Currency currency = Currency.getInstance("UZS");
        currencyFormat.setCurrency(currency);

        // Format a monetary amount
        double amount = 12345.67;
        String formattedMoney = currencyFormat.format(amount);

        System.out.println("Formatted currency: " + formattedMoney); // Output: 12 345,67 UZS
    }
}

```

# Список типов правил

Here's the full list of ICU4J rule types, now updated with a concise code example for each:

---

### 1. **Plural Rules**
Define plural forms based on numerical values.

```java
import com.ibm.icu.text.PluralRules;

PluralRules rules = PluralRules.forLocale(new ULocale("uz"));
System.out.println(rules.select(1)); // Output: "one"
System.out.println(rules.select(5)); // Output: "other"
```

---

### 2. **Rule-Based Number Formatting (RBNF)**
Custom formatting for numbers (e.g., spell out numbers).

```java
import com.ibm.icu.text.RuleBasedNumberFormat;

RuleBasedNumberFormat rbnf = new RuleBasedNumberFormat(RuleBasedNumberFormat.SPELLOUT);
System.out.println(rbnf.format(42)); // Output: "forty-two"
```

---

### 3. **Collation Rules**
Define string sorting orders.

```java
import com.ibm.icu.text.Collator;
import com.ibm.icu.util.ULocale;

Collator collator = Collator.getInstance(new ULocale("uz"));
System.out.println(collator.compare("o'zbek", "özbek")); // Output: -1
```

---

### 4. **Date/Time Formatting Rules**
Customize locale-specific date/time patterns.

```java
import com.ibm.icu.text.DateFormat;
import com.ibm.icu.util.ULocale;

DateFormat df = DateFormat.getDateInstance(DateFormat.FULL, new ULocale("uz"));
System.out.println(df.format(System.currentTimeMillis())); // Output: "29 март 2025 йил"
```

---

### 5. **Message Formatting Rules**
Format messages with placeholders and pluralization.

```java
import com.ibm.icu.text.MessageFormat;
import java.util.HashMap;
import java.util.Map;

Map<String, Object> args = new HashMap<>();
args.put("count", 3);
System.out.println(new MessageFormat("{count, plural, one {# файл} other {# файллар}}", new ULocale("uz")).format(args)); 
// Output: "3 файллар"
```

---

### 6. **Currency Formatting**
Format monetary values based on locale.

```java
import com.ibm.icu.text.NumberFormat;
import com.ibm.icu.util.Currency;
import com.ibm.icu.util.ULocale;

NumberFormat currencyFormat = NumberFormat.getCurrencyInstance(new ULocale("uz"));
currencyFormat.setCurrency(Currency.getInstance("UZS"));
System.out.println(currencyFormat.format(12345.67)); // Output: "12 345,67 UZS"
```

---

### 7. **Transliteration Rules**
Transform text from one script to another.

```java
import com.ibm.icu.text.Transliterator;

Transliterator transliterator = Transliterator.getInstance("Latin-Cyrillic");
System.out.println(transliterator.transliterate("o'zbek")); // Output: "ўзбек"
```

---

### 8. **Break Iteration Rules**
Segment text into words or sentences.

```java
import com.ibm.icu.text.BreakIterator;
import com.ibm.icu.util.ULocale;

BreakIterator wordIterator = BreakIterator.getWordInstance(new ULocale("uz"));
wordIterator.setText("Bu matn misoli.");
while (wordIterator.next() != BreakIterator.DONE) {
    System.out.println(wordIterator.getText()); // Outputs words in "Bu matn misoli."
}
```

---

### 9. **Locale Data and Customization**
Access or modify built-in locale data.

```java
import com.ibm.icu.util.ULocale;

ULocale locale = new ULocale("uz");
System.out.println(locale.getDisplayName()); // Output: "узбек"
```

---

### 10. **Units Formatting**
Format measurements like distance, weight, etc.

```java
import com.ibm.icu.text.MeasureFormat;
import com.ibm.icu.util.Measure;
import com.ibm.icu.util.MeasureUnit;
import com.ibm.icu.util.ULocale;

MeasureFormat measureFormat = MeasureFormat.getInstance(new ULocale("uz"), MeasureFormat.FormatWidth.WIDE);
System.out.println(measureFormat.format(new Measure(25, MeasureUnit.KILOMETER))); // Output: "25 километр"
```

---

### 11. **Text Directionality**
Manage bidirectional text.

```java
import com.ibm.icu.text.Bidi;

Bidi bidi = new Bidi("Text in English متن به فارسی.", Bidi.DIRECTION_DEFAULT_LEFT_TO_RIGHT);
System.out.println(bidi.writeReordered(Bidi.DO_MIRRORING)); // Output: "Text in English .متن به فارسی"
```

---

### 12. **Charset/Encoding Support**
Convert text between encodings.

```java
import com.ibm.icu.text.CharsetDetector;

CharsetDetector detector = new CharsetDetector();
detector.setText("Тест".getBytes());
System.out.println(detector.detect().getName()); // Output: "UTF-8"
```

---

### 13. **Time Zones**
Handle time zone formatting.

```java
import com.ibm.icu.util.TimeZone;

TimeZone timeZone = TimeZone.getTimeZone("Asia/Tashkent");
System.out.println(timeZone.getDisplayName()); // Output: "Тошкент вақти"
```

---

### 14. **Numbering Systems**
Switch numbering systems (e.g., Arabic, Devanagari).

```java
import com.ibm.icu.text.NumberFormat;
import com.ibm.icu.util.ULocale;

NumberFormat arabicNum = NumberFormat.getInstance(new ULocale("ar"));
System.out.println(arabicNum.format(123)); // Output: "١٢٣"
```

---

This expanded list now includes a short Java example for each type of ICU4J rule, demonstrating how to apply the feature in a program. Let me know which you'd like to explore further!
