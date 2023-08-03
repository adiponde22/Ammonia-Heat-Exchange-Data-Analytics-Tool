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

### Real-Time Thermal Performance Metrics Calculation

The program computes the energy transferred in the form of heat (BTU) in real-time. This is done through the `PLC_PRG` function, which applies the formula:

`Q=Cp*m_dot*(T_inlet-T_outlet)`

Where `Q` is the heat transfer, `Cp` is the specific heat capacity, `m_dot` is the mass flow rate, and `T_inlet` and `T_outlet` are the water inlet and outlet temperatures, respectively. The `cp_calculation` function computes the specific heat capacity of the water through a 6th-degree polynomial.

### Data Logging and Transfer

All sensor data and calculated parameters are stored in a text file for historical tracking and subsequent data analysis. This data forms the basis for system performance analysis, energy efficiency monitoring, and predictive maintenance strategies.

The program also includes an FTP function, enabling the recorded data to be transferred to a remote server. This allows for regular checkups, data backups, and further analysis, ensuring the integrity and availability of the data.

### Data Visualization 

The modern, user-friendly UI presents all sensor data and thermal performance metrics in an easy-to-read format. Graphs plotting sensor data over time offer users a powerful tool for pattern recognition and performance optimization. The system also includes an HMI that provides a visual interface for real-time system monitoring and interaction.

## Getting Started

To get started with this tool, ensure the WAGO PFC200 controller is connected to all necessary sensors and that you have a system running CoDeSys. For the FTP feature, you need to have access to a remote server that can receive and store the data. Ensure that the HMI is correctly set up to display the web visualization of the PLC system.

## Conclusion and Future Directions

By leveraging the power of real-time data analysis and remote data transfer, this tool is poised to revolutionize how industry professionals maintain and optimize their ammonia heat exchanger systems. The integration of a dedicated HMI significantly enhances the user experience by providing an intuitive interface for real-time system monitoring and interaction. Looking forward, potential enhancements could include the implementation of alarms for abnormal operating conditions, further UI enhancements for improved data visualization, and the integration of machine learning algorithms for predictive maintenance and anomaly detection.
