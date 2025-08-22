# Feature: Advanced Color Filtering for Live Microscopy Preview

## 1. Description

This feature enhances the live camera preview with advanced color filtering capabilities, specifically tailored for microscopy applications. Users will be able to define one or more color ranges dynamically. Pixels in the live feed falling within any of these defined ranges will be displayed, while pixels outside all defined ranges will be rendered black. This allows users to isolate and highlight specific features of interest in a microscopy view based on their color properties.

## 2. Why (Purpose & Benefit)

- **Enhanced Visual Analysis:** Helps users to focus on specific structures or substances in a sample by filtering out irrelevant visual information.
- **Dynamic Exploration:** Allows for real-time adjustment and refinement of color filters, enabling interactive exploration of the sample.
- **Customization:** Provides the flexibility to define, save, and manage multiple filters and sets of filters tailored to different samples or analysis goals.
- **Improved Focus:** By masking uninteresting areas, users can more easily identify and study features that match the filter criteria.

## 3. Requirements

### 3.1. Functional Requirements

*   **FR1: Live Preview Base:**
    *   The system must display a real-time camera feed.
*   **FR2: Color Range Definition by Pixel Selection:**
    *   Users must be able to tap a pixel on the live preview.
    *   The RGB values of the tapped pixel will serve as the initial center for a new color filter range.
*   **FR3: Interactive Color Range Adjustment:**
    *   For each color filter, users must be able to adjust the **minimum and maximum values** for each RGB channel (Red, Green, Blue) using sliders.
    *   A predefined default window/range should be applied when a color is first selected.
*   **FR4: Real-time Preview Update:**
    *   The live preview must immediately reflect changes made to filter ranges or their enabled/disabled status.
*   **FR5: Filter Application Logic (OR Mask):**
    *   If one or more filters are active, pixels whose RGB values fall within the defined range of *any* active filter will be displayed normally.
    *   Pixels whose RGB values do not fall within the range of *any* active filter will be rendered completely black.
*   **FR6: Filter List Management:**
    *   A UI element must list all currently defined color filters for the active set.
    *   Each filter in the list must display:
        *   Its name (e.g., "ColorFilter1", "Myeloid Cells").
        *   An eye icon (or similar) to toggle the filter on/off.
        *   A trashbin icon (or similar) to remove the filter from the list.
*   **FR7: Add New Filter:**
    *   A "+" icon (or similar) must be available in the filter list UI to allow users to add a new color filter (initiating from FR2).
*   **FR8: Rename Filters:**
    *   Users must be able to tap on a filter's name in the list to edit and assign a custom, meaningful name. Default names should be "ColorFilter1", "ColorFilter2", etc.
*   **FR9: Filter Set Management:**
    *   **FR9.1: Save Filter Set:** Users must be able to save the current collection of defined filters as a named "Set".
    *   **FR9.2: Load Filter Set:** (Implicit) Users must be able to load a previously saved filter set.
    *   **FR9.3: Rename Filter Set:** Users must be able to rename filter sets. Default names should be "Set1", "Set2", etc.
    *   **FR9.4: Active Set Display:** The name of the currently active/loaded filter set must be displayed, preferably at the top of the filter management UI.
    *   **FR9.5: Create New Filter Set:** (Implicit) Users should be able to create a new, empty filter set or duplicate an existing one.

### 3.2. Technical Requirements

*   **TR1: Platform:** The feature will be implemented within an Android application, forked from OpenCamera.
*   **TR2: Performance:**
    *   Filter application and preview updates must be performed in real-time with minimal latency to ensure a smooth user experience.
*   **TR3: UI/UX:**
    *   The new UI elements (sliders, filter list, buttons) must be integrated intuitively within the existing application interface.
    *   Controls should be responsive and easy to use.
*   **TR4: Data Persistence:**
    *   User-defined filters and filter sets (including their names, ranges, and on/off states) must be persistently stored on the device.
*   **TR5: Image Processing:**
    *   Efficient algorithms for color space analysis and pixel manipulation on the live camera frames are required.

## 4. Non-Functional Requirements (Considerations)

*   **NFR1: Usability:** The feature should be easy to learn and intuitive for users familiar with microscopy and digital imaging.
*   **NFR2: Extensibility:** The design should allow for potential future enhancements, such as different masking modes (e.g., AND, XOR) or more complex filter parameters.

## 5. Work Breakdown (Proposed Steps)

1.  **UI Scaffolding & Navigation:**
    *   Identify how OpenCamera handles its settings menu or on-screen controls.
    *   Add a new menu item or icon to access the "Advanced Color Filtering" mode/panel.
    *   Create a basic UI layout for the filter management panel (placeholder for filter list, set name, add button).
2.  **Pixel Color Picking (FR2):**
    *   Implement functionality to capture touch events on the live preview.
    *   Retrieve the RGB color of the selected pixel.
    *   Display these RGB values (initially, perhaps in a toast or a simple text view for verification).
3.  **Basic Filter Data Structure & Display (FR6):**
    *   Define a data class/structure to hold a single color filter's information (e.g., name, **R-min, R-max, G-min, G-max, B-min, B-max**, isEnabled).
    *   Implement a simple list UI (e.g., RecyclerView) to display names of added filters.
    *   When a pixel is picked (Step 2), create a new filter object with default window/ranges based on the picked color and add it to this list.
4.  **Core Image Processing - Single Filter Application (FR3, FR4, FR5 - partial):**
    *   Identify how to access and modify live camera preview frames in OpenCamera (e.g., `onPreviewFrame` callback, CameraX ImageAnalysis, or OpenGL shaders).
    *   Implement the image processing logic to:
        *   Iterate through pixels of the frame.
        *   For a *single, initially hardcoded or the first* filter in the list: if the pixel's RGB values fall within the filter's defined range, display it; otherwise, render it black.
    *   Ensure the preview updates in real-time to reflect the filter.
5.  **Interactive Range Adjustment UI & Logic (FR3):**
    *   For the currently selected/active filter, add UI elements (e.g., **6 sliders, two for each RGB channel, representing the minimum and maximum values for that channel**).
    *   Link these sliders to the selected filter's range properties in its data structure.
    *   Ensure changes from sliders trigger an immediate reprocessing of the current frame and update of the live preview.
6.  **Filter List Management - Full CRUD (FR6, FR7, FR8):**
    *   Implement the "+" button in the UI to trigger the pixel picking flow (Step 2) and add a new filter to the list.
    *   Implement the "eye" icon toggle for each filter in the list to enable/disable it. The image processing logic (Step 4) must respect this state.
    *   Implement the "trashbin" icon to remove a filter from the list and update the preview.
    *   Implement renaming of filters: tap on a filter's name in the list, show an input dialog, and update the filter's name in the data structure and UI.
7.  **Multiple Filter Application Logic (OR Mask) (FR5 - full):**
    *   Modify the image processing logic (from Step 4) to iterate through *all enabled* filters in the current list.
    *   A pixel is displayed if its RGB values fall within the defined range of *any* of the enabled filters.
8.  **Filter Set Data Structure & Basic UI (FR9.4):**
    *   Define a data class/structure for a "Filter Set" (e.g., set name, list of filter objects).
    *   Display the name of the currently active/loaded filter set in the UI (e.g., at the top of the filter management panel). Start with a default name like "Default Set".
9.  **Filter Set Persistence (FR9.1, FR9.2, TR4):**
    *   Implement functionality to save the current filter set (including all its filters, their names, ranges, and on/off states) to persistent storage on the device (e.g., using SharedPreferences with JSON, or a simple local database like Room).
    *   Implement functionality to load a previously saved filter set, making it the active set and updating the UI and live preview accordingly.
    *   Provide UI for managing saved sets (e.g., a dropdown to select a set to load, a button to save the current configuration as a new set).
10. **Filter Set Management - Naming and Creation (FR9.3, FR9.5):**
    *   Implement renaming of saved filter sets.
    *   Implement creating a new (empty) filter set or duplicating an existing one to start a new configuration.
11. **Refinement, Optimization, and UI Integration (TR2, TR3, NFR1):**
    *   Focus on optimizing the image processing algorithms for performance and minimizing battery consumption.
    *   Refine the UI/UX for intuitiveness, ease of use, and seamless integration with the existing OpenCamera interface.
    *   Ensure all controls are responsive and provide good user feedback.
12. **Thorough Testing and Bug Fixing:**
    *   Conduct comprehensive testing of all functionalities across different scenarios, including edge cases.
    *   Test on various Android devices and versions, if possible.
    *   Identify and fix any bugs, performance bottlenecks, or usability issues.
