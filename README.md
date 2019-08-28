# racecar_simulator_changed_files
racecar simulator changed files

Changed File

## node behavior_convtroller.cpp

1. mux index, button index, key index 변수 추가

```c++
    // Mux indices
    int joy_mux_idx;
    int key_mux_idx;
    int safety_copilot_mux_idx;
    int random_walker_mux_idx;
    // Add pure pursuit mux idx
    int pure_pursuit_mux_idx;

    // Mux controller array
    std::vector<bool> mux_controller;
    int mux_size;

    // Button indices
    int joy_button_idx;
    int key_button_idx;
    int random_walk_button_idx;
    int safety_copilot_button_idx;
    // Add pure pursuit button idx
    int pure_pursuit_button_idx;

    // Key indices
    std::string joy_key_char;
    std::string keyboard_key_char;
    std::string copilot_key_char;
    std::string random_walk_key_char;
    // Add pure pursuit key inx
    std::string pure_pursuit_key_char;
```
