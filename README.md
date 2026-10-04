# FleetCheck – Build Systems Lab

This project is intentionally incomplete. Follow the worksheet in the order given.

Expected final application output:

```
FleetCheck 1.0
Vehicles loaded: 4
Vehicles requiring service: 2
Average mileage: 37000 km
```

Do not copy the solution POM. The objective is to observe how each build change alters the result.
## Evidence 1
[ERROR] /C:/Users/Diogo/Downloads/FleetCheck_Starter/FleetCheck_Starter/src/main/java/pt/upt/fleetcheck/App.java:[4,38] package com.fasterxml.jackson.databind does not exist
O erro vem do import na linha 4 do App.java: `import com.fasterxml.jackson.databind.ObjectMapper;`

## Step 2 – Adicionar o jackson-databind

Adicionei a dependência `jackson-databind` 2.22.2 ao `pom.xml` e corri `mvn clean package`.

**Resultado:** a compilação passou (`BUILD SUCCESS`), ao contrário do Passo 1.

**Desvio em relação ao esperado:** a ficha indica que a fase de testes devia falhar.
No meu caso isso não aconteceu, porque o projeto starter não inclui nenhuma classe
de teste (a pasta `src/test/java/pt/upt/fleetcheck` está vazia). O Surefire não
encontrou testes para executar, por isso não detetou nenhum defeito de comportamento.

## Step 3 – Dependency tree

Executei `mvn dependency:tree`. O `jackson-databind` 2.22.2 é a única dependência
direta de aplicação. O `jackson-core` e o `jackson-annotations` aparecem como
dependências transitivas, trazidas automaticamente pelo `jackson-databind`.
O JUnit tem escopo `test`, por isso não faz parte do artefacto final.

## Step 4 – JAR executável

**JAR normal** (`mvn clean package` + `java -jar target/fleetcheck-1.0.0.jar`):

    no main manifest attribute, in target/fleetcheck-1.0.0.jar

**Com o maven-shade-plugin** (`mvn clean package` + `java -jar target/fleetcheck-1.0.0-all.jar`):

    FleetCheck 1.0 | Vehicles loaded: 4 | Vehicles requiring service: 2 | Average mileage: 37000 km

**Evidence 4 – O que o Shade mudou em relação ao JAR por omissão:**
O JAR normal contém apenas as classes do projeto, não declara a `Main-Class` no manifesto
e não inclui o Jackson, por isso não é executável sozinho. O Shade gera um JAR "gordo"
(`fleetcheck-1.0.0-all.jar`) que copia para dentro as classes das dependências (Jackson)
e escreve a `Main-Class` (`pt.upt.fleetcheck.App`) no manifesto, tornando-o autónomo.