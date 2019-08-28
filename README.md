# racecar_simulator_changed_files
racecar simulator changed files
시뮬레이터 깃 링크 : https://github.com/mlab-upenn/racecar_simulator  
시뮬레이션 코드 구조등은 링크 글 참고
***

Changed Files  
올려둔 수정된 파일과 같이 확인하며 비교, 수정  

## params.yaml

- ```mux_size``` 변경
- ```pure_pursuit_mux_idx``` 추가
- ```pure_pursuit_button_idx``` 추가
- ```pure_pursuit_key_char``` 추가
- ```pure_pursuit_drive_topic``` 추가

쭉 읽어보면서 어디에추가해야는지 확인하고 추가하기  
위에는 시뮬레이션과 pure_pursuit node를 연결하기 위한 작업  
업로드한 파일에서
```yaml
pure_pursuit_button_idx: 7
```
과 같이 설정해두었는데 7 은 RT버튼  
이 버튼을 누르면 pure pursuit 노드에서 오는 데이터로 주행  
자세한 내용은 시뮬레이터 깃헙 글과, 코드 참고
  
  
particle filter에 pose와 odom data를 연결시키기위해 토픽 이름 변경 필요
```yaml
pose_topic: "/vesc/pose"  #/pose
odom_topic: "/vesc/odom"  #/odom
```



## node behavior_convtroller.cpp

1. pure pursuit - mux index, button index, key index 변수 추가
BehaviorController privat 내부

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
  
  
2. BehaviorController 생성자에서 위에 추가한 index들 파라미터에서 읽어오기
```c++
        // Get mux indices
        n.getParam("joy_mux_idx", joy_mux_idx);
        n.getParam("key_mux_idx", key_mux_idx);
        n.getParam("safety_copilot_mux_idx", safety_copilot_mux_idx);
        n.getParam("random_walker_mux_idx", random_walker_mux_idx);
        // Add pure pursuit
        n.getParam("pure_pursuit_mux_idx", pure_pursuit_mux_idx);

        // Get button indices
        n.getParam("joy_button_idx", joy_button_idx);
        n.getParam("key_button_idx", key_button_idx);
        n.getParam("copilot_button_idx", safety_copilot_button_idx);
        n.getParam("random_walk_button_idx", random_walk_button_idx);
        // Add pure pursuit
        n.getParam("pure_pursuit_button_idx", pure_pursuit_button_idx);

        // Get key indices
        n.getParam("joy_key_char", joy_key_char);
        n.getParam("keyboard_key_char", keyboard_key_char);
        n.getParam("copilot_key_char", copilot_key_char);
        n.getParam("random_walk_key_char", random_walk_key_char);
        // Add pure pursuit
        n.getParam("pure_pursuit_key_char", pure_pursuit_key_char);
```
  
  
3. joy_callback 함수와 key_callback함수 마지막에 else if 문으로 변경하고, new를 pure_pursuit으로 변경(기존코드에 주석처리되어있는부분)
  
  
joy_callback()
```c++
        // ***Add new else if statement here for new planning method***
        else if (msg.buttons[pure_pursuit_button_idx]) {
            // new planner
            toggle_mux(pure_pursuit_mux_idx, "Pure_pursuit Planner");
        }
```
  
  
key_callback()
```c++
        // ***Add new else if statement here for new planning method***
        else if (msg.data == pure_pursuit_key_char) {
            // new planner
            toggle_mux(pure_pursuit_mux_idx, "Pure_pursuit Planner");
        }
```
  
  
## mux.cpp

mux 생성자 안에 channel 추가해주기 (주석처리만 지워주고 ```new_*``` 대신 ```pure_pursuit_*```로 변경 ```rosparam```  이름은 ```params.yaml```에서 설정한 이름으로)

```c++
        // ***Add a channel for a new planner here**
        int pure_pursuit_mux_idx;
        std::string pure_pursuit_drive_topic;
        n.getParam("pure_pursuit_drive_topic", pure_pursuit_drive_topic);
        n.getParam("pure_pursuit_mux_idx", pure_pursuit_mux_idx);
        add_channel(pure_pursuit_drive_topic, drive_topic, pure_pursuit_mux_idx);
```
