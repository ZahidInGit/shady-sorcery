Project Proposal: Shady SorceryCourse: Software Engineering / Mobile Application DevelopmentPlatform: Android (Min SDK 26 / Android 8.0 Oreo)Language: JavaArchitecture Pattern: Template Method / Sensor abstraction with modular Activities1. Project Overview & Scope1.1 Executive SummaryShady Sorcery is a multi-tool Android application designed to leverage ambient mobile hardware sensors to perform practical measurements and signal analyses. Featuring a unified 5-card dashboard architecture, the app turns standard smartphone hardware into specialized utilities: a magnetic field detector, a barometric floor altimeter, a micro-seismograph, an infrared light visualizer, and an optical photoplethysmographic (PPG) heart rate monitor.1.2 Core Features & Tech StackLanguage & UI: Java, XML Layouts, Material Design ComponentsHardware Interfacing: SensorManager, CameraX Java API (Front/Rear Camera & Torch control)Visualization: MPAndroidChart for real-time dynamic graphingPermissions: Single runtime permission required (android.permission.CAMERA)Feature Breakdown:Metal Detector (TYPE_MAGNETIC_FIELD): Monitors field magnitude in microteslas ($\mu\text{T}$), displaying strength via a color-coded visual gauge and dynamic audio feedback.Floor Detector (TYPE_PRESSURE): Calculates relative floor elevation offsets using barometric pressure formulas and baseline ground setting.Mini Seismograph (TYPE_ACCELEROMETER): Isolates high-frequency vibration via digital filtering, displaying real-time linear acceleration graphs and peak alerts.IR Viewer (CameraX Front Camera): Captures high-frame-rate infrared signals (emitted by remotes) unfiltered by standard rear-camera IR blockers.Heart Rate Monitor (CameraX Rear + Flash): Employs optical absorption analysis on the red color channel across frame buffer feeds to detect cardiac pulse peaks and compute BPM.2. Software Process Model & Schedule2.1 Process Model Selection: Agile Scrum MethodologyThe project follows an iterative 4-week Agile methodology across 2-week Sprint cycles. This structure accommodates hardware-software integration risks and real-time filter calibration.+-------------------------------------------------------------------------------+
| PHASE 1: Requirements & Architecture (Week 1)                                |
|   +-- Setup Android Studio, dependencies, and BaseSensorActivity pattern      |
+-------------------------------------------------------------------------------+
                                      |
                                      v
+-------------------------------------------------------------------------------+
| PHASE 2: Core Sensor Development - Sprint 1 (Week 2)                           |
|   +-- Metal Detector, Floor Altimeter, & LowPassFilter implementation         |
+-------------------------------------------------------------------------------+
                                      |
                                      v
+-------------------------------------------------------------------------------+
| PHASE 3: CameraX & Dynamic Graphing - Sprint 2 (Week 3)                       |
|   +-- Seismograph (MPAndroidChart), IR Viewer, and PPG Heart Rate Monitor     |
+-------------------------------------------------------------------------------+
                                      |
                                      v
+-------------------------------------------------------------------------------+
| PHASE 4: Testing, Refactoring, & Documentation (Week 4)                        |
|   +-- System Integration, Code Polish, and Final UML Alignment               |
+-------------------------------------------------------------------------------+
2.2 Project Milestones & DeliverablesM1 (End of Week 1): System Architecture & Base Framework (BaseSensorActivity, Navigation grid).M2 (End of Week 2): Motion & Atmospheric Sensor Modules operational.M3 (End of Week 3): Vision & Optical Signal Processing Engine integrated.M4 (End of Week 4): Final Project Proposal, Unit Test Execution, and Release Build.2.3 Gantt Chart (WBS Breakdown)gantt
    dateFormat  YYYY-MM-DD
    title Shady Sorcery Development Roadmap
    
    section Requirements & Setup
    Project Proposal & Setup    :a1, 2026-11-01, 4d
    Base Architecture Design    :a2, 2026-11-03, 4d
    
    section Sprint 1: Physical Sensors
    Metal Detector Module       :b1, 2026-11-08, 4d
    Floor Altimeter Module      :b2, 2026-11-10, 4d
    LowPassFilter Utility       :b3, 2026-11-12, 3d
    
    section Sprint 2: Vision & Audio
    Mini Seismograph + Graphs  :c1, 2026-11-15, 5d
    IR Viewer Integration       :c2, 2026-11-17, 4d
    PPG Heart Rate Monitor      :c3, 2026-11-20, 5d
    
    section Final Phase
    System Integration & Test   :d1, 2026-11-25, 4d
    Documentation & Submission  :d2, 2026-11-27, 3d
3. UML Diagram Specifications3.1 Use Case Diagram (PlantUML)@startuml
left to right direction
actor User
rectangle "Shady Sorcery App" {
    usecase "View Dashboard" as UC_Dashboard
    usecase "Measure Magnetic Field" as UC_Metal
    usecase "Calculate Floor Offset" as UC_Floor
    usecase "Monitor Vibration Waves" as UC_Seismo
    usecase "Inspect IR Emmitters" as UC_IR
    usecase "Measure Heart Rate (PPG)" as UC_Heart
    usecase "Grant Camera Permission" as UC_Perm
}

User --> UC_Dashboard
UC_Dashboard ..> UC_Metal : <<select>>
UC_Dashboard ..> UC_Floor : <<select>>
UC_Dashboard ..> UC_Seismo : <<select>>
UC_Dashboard ..> UC_IR : <<select>>
UC_Dashboard ..> UC_Heart : <<select>>

UC_IR ..> UC_Perm : <<include>>
UC_Heart ..> UC_Perm : <<include>>
@enduml
3.2 Data Flow Diagram (DFD Level 1)                       +-----------------------+
                       | Physical Sensors &    |
                       | CameraX Frame Buffers |
                       +-----------------------+
                                   |
                                   | Raw Sensor Event Signals (x, y, z, pressure, frames)
                                   v
+--------+  Select Tool  +-------------------+  Filtered Data   +--------------------+
|        |-------------->|  1.0 Sensor Data  |----------------->| 2.0 Signal & Image |
|  User  |               |    Dispatcher     |                  |     Processing     |
|        |<--------------| (BaseSensorAct)   |                  | (LowPass / PPG)    |
+--------+ Rendered Visuals+-------------------+                  +--------------------+
               ^                                                           |
               |                                                           | Processed Values
               +-----------------------------------------------------------+
3.3 Activity Diagram (Heart Rate Measurement Flow)@startuml
start
:User selects Heart Rate Monitor;
if (Camera Permission Granted?) then (No)
  :Request Camera Permission;
  if (Permission Allowed?) then (No)
    :Display Error & Return to Home;
    stop
  else (Yes)
  endif
else (Yes)
endif

:Initialize CameraX & Turn On Flashlight;
:Display "Place Finger on Camera" Prompt;

repeat
  :Capture Frame Buffer;
  :Calculate Average Red Channel Intensity;
  :Push Value to Rolling Queue;
  :Apply Signal Pass Filter;
  :Update Live Pulse Waveform;
backward:Continue Stream;
repeat while (15 Seconds Elapsed?) is (No)

:Identify Systolic Peaks;
:Compute Beats Per Minute (BPM);
:Display Final Heart Rate;
:Turn Off Flashlight;
stop
@enduml
3.4 Entity-Relationship Diagram (ERD - Local Persistence Data Model)@startuml
entity "Session" {
    * session_id : INTEGER <<PK>>
    --
    timestamp : DATETIME
    tool_type : VARCHAR(30)
}

entity "Measurement" {
    * measurement_id : INTEGER <<PK>>
    --
    # session_id : INTEGER <<FK>>
    raw_value : FLOAT
    processed_value : FLOAT
    unit : VARCHAR(10)
    timestamp_ms : BIGINT
}

Session ||--|{ Measurement : "generates"
@enduml
3.5 Class Diagram@startuml
abstract class BaseSensorActivity {
    # sensorManager : SensorManager
    # isSensorAvailable : boolean
    # abstract getTargetSensorType() : int
    # abstract onSensorDataReceived(SensorEvent) : void
    # onCreate(Bundle) : void
    # onResume() : void
    # onPause() : void
}

class MainActivity {
    - recyclerView : RecyclerView
    - adapter : CardAdapter
    + onCreate(Bundle) : void
}

class LowPassFilter {
    - ALPHA : float
    + filter(current : float[], output : float[]) : float[]
    + highPass(current : float[], output : float[]) : float[]
}

class MetalDetectorActivity {
    - magneticSensor : Sensor
    - toneGenerator : ToneGenerator
    + getTargetSensorType() : int
    + onSensorDataReceived(SensorEvent) : void
}

class HeartRateActivity {
    - cameraProvider : ProcessCameraProvider
    - bpmCalculator : PPGCalculator
    + getTargetSensorType() : int
    + onSensorDataReceived(SensorEvent) : void
    - processImageFrame(ImageProxy) : void
}

MainActivity --> BaseSensorActivity : Launches
BaseSensorActivity <|-- MetalDetectorActivity
BaseSensorActivity <|-- HeartRateActivity
BaseSensorActivity --> LowPassFilter : Uses
@enduml
3.6 Object Diagram (Runtime Instance Context)@startuml
object "mainDashboard : MainActivity" as main {
    activeScreen = "Home"
}

object "seismoActivity : SeismographActivity" as seismo {
    isSensorAvailable = true
    sensorType = 1 (TYPE_ACCELEROMETER)
}

object "signalFilter : LowPassFilter" as filter {
    ALPHA = 0.15f
}

object "accelSensor : Sensor" as sensor {
    name = "Linear Acceleration Sensor"
    vendor = "Bosch"
}

seismo -- main : spawned by
seismo -- filter : processes with
seismo -- sensor : listens to
@enduml
4. Class Interface Implementations (Java Source Code)4.1 BaseSensorActivity.javapackage com.shadysorcery.app;

import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

/**
 * Base abstract activity handling sensor lifecycles.
 */
public abstract class BaseSensorActivity extends AppCompatActivity implements SensorEventListener {

    protected SensorManager sensorManager;
    private Sensor targetSensor;
    protected boolean isSensorAvailable;

    protected abstract int getTargetSensorType();
    protected abstract void onSensorDataReceived(SensorEvent event);

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        sensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
        
        if (getTargetSensorType() != -1) {
            targetSensor = sensorManager.getDefaultSensor(getTargetSensorType());
            isSensorAvailable = (targetSensor != null);
        } else {
            isSensorAvailable = true; // For non-SensorManager features like CameraX
        }
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (targetSensor != null && isSensorAvailable) {
            sensorManager.registerListener(this, targetSensor, SensorManager.SENSOR_DELAY_UI);
        } else if (getTargetSensorType() != -1 && !isSensorAvailable) {
            showSensorUnavailableError();
        }
    }

    @Override
    protected void onPause() {
        super.onPause();
        if (targetSensor != null) {
            sensorManager.unregisterListener(this);
        }
    }

    @Override
    public void onSensorChanged(SensorEvent event) {
        if (event.sensor.getType() == getTargetSensorType()) {
            onSensorDataReceived(event);
        }
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        // Concrete activities can override if needed
    }

    protected void showSensorUnavailableError() {
        Toast.makeText(this, "Required hardware sensor is missing on this device.", Toast.LENGTH_LONG).show();
    }
}
4.2 LowPassFilter.javapackage com.shadysorcery.app;

/**
 * Utility class for signal processing and smoothing sensor jitter.
 */
public class LowPassFilter {

    private static final float DEFAULT_ALPHA = 0.25f;

    private LowPassFilter() {
        // Utility class
    }

    /**
     * Applies a low-pass filter to raw input signals.
     */
    public static float[] filter(float[] input, float[] output, float alpha) {
        if (output == null) return input.clone();
        for (int i = 0; i < input.length; i++) {
            output[i] = output[i] + alpha * (input[i] - output[i]);
        }
        return output;
    }

    /**
     * Applies a high-pass filter by subtracting low-pass smoothed components.
     */
    public static float[] highPass(float[] input, float[] output, float alpha) {
        float[] smoothed = filter(input, output, alpha);
        float[] highPassResult = new float[input.length];
        for (int i = 0; i < input.length; i++) {
            highPassResult[i] = input[i] - smoothed[i];
        }
        return highPassResult;
    }
}
4.3 MetalDetectorActivity.javapackage com.shadysorcery.app;

import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.os.Bundle;
import android.widget.TextView;
import androidx.annotation.Nullable;

/**
 * Metal Detector module analyzing magnetic field magnitude.
 */
public class MetalDetectorActivity extends BaseSensorActivity {

    private TextView tvMagnitude;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_metal_detector);
        tvMagnitude = findViewById(R.id.tv_magnitude);
    }

    @Override
    protected int getTargetSensorType() {
        return Sensor.TYPE_MAGNETIC_FIELD;
    }

    @Override
    protected void onSensorDataReceived(SensorEvent event) {
        float x = event.values[0];
        float y = event.values[1];
        float z = event.values[2];

        // Compute magnetic flux density magnitude: |B| = sqrt(x^2 + y^2 + z^2)
        double magnitude = Math.sqrt(x * x + y * y + z * z);

        if (tvMagnitude != null) {
            tvMagnitude.setText(String.format("%.2f µT", magnitude));
        }
    }
}
4.4 FloorDetectorActivity.javapackage com.shadysorcery.app;

import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.annotation.Nullable;

/**
 * Barometric pressure module computing relative floor elevation offsets.
 */
public class FloorDetectorActivity extends BaseSensorActivity {

    private TextView tvElevationOffset;
    private Button btnSetGround;
    private float groundPressure = SensorManager.PRESSURE_STANDARD_ATMOSPHERE;
    private boolean isGroundSet = false;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_floor_detector);

        tvElevationOffset = findViewById(R.id.tv_elevation_offset);
        btnSetGround = findViewById(R.id.btn_set_ground);

        btnSetGround.setOnClickListener(v -> isGroundSet = false);
    }

    @Override
    protected int getTargetSensorType() {
        return Sensor.TYPE_PRESSURE;
    }

    @Override
    protected void onSensorDataReceived(SensorEvent event) {
        float currentPressure = event.values[0];

        if (!isGroundSet) {
            groundPressure = currentPressure;
            isGroundSet = true;
        }

        // Calculate altitude relative to calibrated baseline
        float groundAltitude = SensorManager.getAltitude(SensorManager.PRESSURE_STANDARD_ATMOSPHERE, groundPressure);
        float currentAltitude = SensorManager.getAltitude(SensorManager.PRESSURE_STANDARD_ATMOSPHERE, currentPressure);
        float relativeOffsetMeters = currentAltitude - groundAltitude;

        tvElevationOffset.setText(String.format("%.1f m relative elevation", relativeOffsetMeters));
    }
}
4.5 SeismographActivity.javapackage com.shadysorcery.app;

import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.os.Bundle;
import android.widget.TextView;
import androidx.annotation.Nullable;

/**
 * High-pass filtered accelerometer monitoring real-time seismic vibration magnitude.
 */
public class SeismographActivity extends BaseSensorActivity {

    private TextView tvPeakAcceleration;
    private float[] gravityOutput = new float[3];
    private float maxPeak = 0.0f;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_seismograph);
        tvPeakAcceleration = findViewById(R.id.tv_peak_acceleration);
    }

    @Override
    protected int getTargetSensorType() {
        return Sensor.TYPE_ACCELEROMETER;
    }

    @Override
    protected void onSensorDataReceived(SensorEvent event) {
        // Isolate dynamic acceleration using High-Pass Filter
        float[] linearAcceleration = LowPassFilter.highPass(event.values, gravityOutput, 0.15f);

        float netVibration = (float) Math.sqrt(
                linearAcceleration[0] * linearAcceleration[0] +
                linearAcceleration[1] * linearAcceleration[1] +
                linearAcceleration[2] * linearAcceleration[2]
        );

        if (netVibration > maxPeak) {
            maxPeak = netVibration;
        }

        tvPeakAcceleration.setText(String.format("Peak Acceleration: %.2f m/s²", maxPeak));
    }
}
