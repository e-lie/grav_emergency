---
title: 'Exercices Corrigés partie 1'
visible: false
---

## Exercice 7
```java
public class Exercice7{
    public static String table_de_multiplication(int nombre){
        String result = String.format("| Table de %d \n--------------\n", nombre);

        for(int n = 0; n < 10; n++){
            result +=  String.format("| %d x %d = %d \n", n, nombre, n * nombre);
        }

        return result;
    }
    public static void main(String[] args) {
        System.out.println(Exercice7.table_de_multiplication(7));
    }
}
```