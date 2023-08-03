# Ammonia Heat Exchanger Data Analytics Tool

This repository contains a CoDeSys-based program designed for an ammonia heat exchanger system used in industrial refrigeration processes. The tool uses real-time data from a network of sensors interfaced with a WAGO PFC200 controller to compute important thermal performance metrics, facilitate data-driven decision making, and aid in predictive maintenance strategies. Additionally, it features a data transfer mechanism using FTP for backing up and remotely accessing the data for regular checkups. An integral part of the project is the use of an HMI (Human Machine Interface) for intuitive data visualization and interaction.

## Overview

The ammonia heat exchanger is a critical component in refrigeration operations, where maintaining optimal performance is paramount for energy efficiency and operational effectiveness. This tool serves to optimize this process by calculating the heat energy transferred using sensor data, tracking historical performance, presenting the data in an easily interpretable form to the end-user, allowing for remote access and backups of data, and displaying information through a dedicated HMI.

## Hardware

The WAGO PFC200 controller acts as the data collection hub for the system. It interfaces with the following sensor array:
- Eight temperature probes
- Two pressure probes
- One flow meter

These sensors are all analog types that output a 4-20mA signal.

The HMI component is a Linux-based embedded computer panel that displays the web visualization of the WAGO PFC200 PLC system on a local network. The HMI is set up to directly boot into the web visualization of the PLC, providing a user-friendly interface for system monitoring and interaction.

## Software 

The CoDeSys program performs several tasks, namely:

### Data Acquisition and Conditioning

The sensors' analog signals are transformed into usable process values through a linear function. A calibration page allows users to apply specific offsets as needed, thereby enabling more accurate data representation.



## Function Descriptions

This section provides an overview of the key functions employed in this CoDeSys program.

### `analog_conv(FUN)`

This function is crucial in converting the analog signals from the sensors into usable data. It uses a linear function to map the 4-20mA signal into a range that represents the actual physical quantity being measured (temperature, pressure, or flow rate). The function follows this structure:

`output = (X1 - X0) / 32768 * input + (X0 + offset)`

- `input`: The raw sensor reading in the range 4-20mA.
- `X0` and `X1`: These are calibration parameters that are used to map the 4-20mA range onto the physical range of the parameter being measured.
- `offset`: This is an additional user-defined offset that can be set for each sensor individually to account for any systematic error.

### `cp_calculation(FUN)`

The `cp_calculation` function is an advanced, machine learning-enabled feature that calculates the specific heat capacity of water (`Cp`) in real-time. It's machine learning model is a polynomial regression based model that captures non-linear relationships between varying density of water at different temperatures to provide a highly accurate, real-time estimate of `Cp`.

The function is computed as a 6th degree polynomial regression model, with the equation and coefficients provided below:

```plaintext
X := (INLET_TEMP + OUTLET_TEMP) / 2

CP := 1.03 + (-0.0013 * X) + 2.1 * (EXPT(10, -5)) * (EXPT(X, 2)) + (-1.82 * (EXPT(10, -7)) * EXPT(X, 3)) + 9.25 * EXPT(10, -10) * EXPT(X, 4) + (-2.54 * EXPT(10, -12) * EXPT(X, 5)) + 2.94 * EXPT(10, -15) * EXPT(X, 6)
```

In this equation:

- `X` is the average temperature of the water, calculated as the mean of the inlet and outlet temperatures.
- `CP` is the specific heat capacity of the water. The coefficients of the polynomial are derived from the machine learning model, which has been trained on empirical data to accurately represent `Cp` across a wide range of operating conditions.
  
This machine learning approach allows for a more accurate, dynamic calculation of `Cp` that can adapt to changes in water temperature, ultimately leading to more precise energy transfer rate (`Q`) computations.

### `PLC_PRG(PRG)`

The `PLC_PRG` function acts as the main orchestrator of the program. It reads and translates all the data from the sensors, calls the necessary functions to compute Cp and convert analog signals, and finally calculates the heat transfer rate (Q) using the formula:

`Q = Cp * m_dot * (T_inlet - T_outlet)`

- `Cp`: Specific heat capacity of the water, calculated using `cp_calculation(FUN)`
- `m_dot`: Mass flow rate of the water
- `T_inlet` and `T_outlet`: Inlet and outlet temperatures of the water, measured by the temperature probes

All sensor data and calculated parameters are then saved to a text file for subsequent analysis. The program also initiates the data transfer to the remote server using FTP, ensuring the backup and availability of the data.

## Data Visualization 

The program uses a modern UI to display real-time sensor data and calculated performance metrics in a user-friendly format. This includes both raw numerical data and graphical representations to help users recognize patterns and understand system performance over time.

## Calibration

To cater to individual system nuances and enhance data accuracy, the program provides a calibration page. Users can input specific offsets for each sensor to account for any systematic error, ensuring the accurate representation of the system's physical quantities.


## Real-Time Thermal Performance Metrics Calculation

The program computes the energy transferred in the form of heat (BTU) in real-time. This is done through the `PLC_PRG` function, which applies the formula:

`Q=Cp*m_dot*(T_inlet-T_outlet)`

Where `Q` is the heat transfer, `Cp` is the specific heat capacity, `m_dot` is the mass flow rate, and `T_inlet` and `T_outlet` are the water inlet and outlet temperatures, respectively. The `cp_calculation` function computes the specific heat capacity of the water through a 6th-degree polynomial.

## Data Logging and Transfer

All sensor data and calculated parameters are stored in a text file for historical tracking and subsequent data analysis. This data forms the basis for system performance analysis, energy efficiency monitoring, and predictive maintenance strategies.

The program also includes an FTP function, enabling the recorded data to be transferred to a remote server. This allows for regular checkups, data backups, and further analysis, ensuring the integrity and availability of the data.

## Data Visualization 

The modern, user-friendly UI presents all sensor data and thermal performance metrics in an easy-to-read format. Graphs plotting sensor data over time offer users a powerful tool for pattern recognition and performance optimization. The system also includes an HMI that provides a visual interface for real-time system monitoring and interaction.

## Conclusion and Future Directions

By leveraging the power of real-time data analysis and remote data transfer, this tool is poised to revolutionize how industry professionals maintain and optimize their ammonia heat exchanger systems. The integration of a dedicated HMI significantly enhances the user experience by providing an intuitive interface for real-time system monitoring and interaction. Looking forward, potential enhancements could include the implementation of alarms for abnormal operating conditions, further UI enhancements for improved data visualization, and the integration of machine learning algorithms for predictive maintenance and anomaly detection.
