# Android-Project 
//Start

1. AndroidManifest.xml
// put this line in this folder 
  <uses-permission android:name="android.permission.RECORD_AUDIO" />

2. colors.xml
<color name="purple_500">#6200EE</color>

3. rounded_button.xml
// creates a button 
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="?attr/colorControlHighlight">
    <item>
        <shape android:shape="rectangle">
            <solid android:color="@color/purple_500"/>
            <corners android:radius="240dp"/>
        </shape>
    </item>
</ripple>

4. activity_main.xml
// complete UI 
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#1F1F1F">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="24dp"
        android:gravity="center_horizontal">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Speech -> Text"
        android:textSize="26sp"
        android:textStyle="bold"
        android:textColor="#FFFFFF"
        android:layout_marginBottom="30dp" />


        <Button
            android:id="@+id/btnRecord"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="🎤 Start Recording"
            android:textAllCaps="false"
            android:textSize="18sp"
            android:textStyle="bold"
            android:padding="14dp"
            android:backgroundTint="@color/purple_500"
            android:textColor="@android:color/white"
            android:layout_marginTop="12dp"
            android:layout_marginBottom="8dp"
            android:background="@drawable/rounded_button" />


        <TextView
        android:id="@+id/textTranscription"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Transcribed text will appear here"
        android:textSize="16sp"
        android:background="#E0E0E0"
        android:padding="16dp"
        android:layout_marginTop="16dp" />

    <Button
        android:id="@+id/btnCopy"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Copy"
        android:layout_marginTop="16dp" />

    </LinearLayout>

</ScrollView>

5. MainActivity.java
// main java code
package com.alphatech.speech_text; // varies as per peoples

import android.content.ClipboardManager;
import android.content.ClipData;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.speech.RecognizerIntent;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

import java.util.ArrayList;
import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private static final int REQUEST_CODE_SPEECH_INPUT = 1;

    private Button btnRecord, btnCopy;
    private TextView textTranscription;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnRecord = findViewById(R.id.btnRecord);
        btnCopy = findViewById(R.id.btnCopy);
        textTranscription = findViewById(R.id.textTranscription);

        btnRecord.setOnClickListener(view -> startSpeechToText());

        btnCopy.setOnClickListener(view -> {
            String textToCopy = textTranscription.getText().toString();
            if (!textToCopy.isEmpty()) {
                ClipboardManager clipboard = (ClipboardManager) getSystemService(Context.CLIPBOARD_SERVICE);
                ClipData clip = ClipData.newPlainText("Transcribed Text", textToCopy);
                clipboard.setPrimaryClip(clip);
                Toast.makeText(MainActivity.this, "Text copied to clipboard", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(MainActivity.this, "No text to copy", Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void startSpeechToText() {
        Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, Locale.getDefault());

        try {
            startActivityForResult(intent, REQUEST_CODE_SPEECH_INPUT);
        } catch (Exception e) {
            Toast.makeText(this, "Speech recognition not supported!", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == REQUEST_CODE_SPEECH_INPUT && resultCode == RESULT_OK && data != null) {
            ArrayList<String> result = data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);
            if (result != null && result.size() > 0) {
                textTranscription.setText(result.get(0));
            }
        }
    }
}

