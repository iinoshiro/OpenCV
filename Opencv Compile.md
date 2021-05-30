<center><h3>
  OpenCV + QT + MacOS 編譯過程 （ 無ＧＰＵ版本 ）
  </h3></center>

1. 到 OpenCV 官方網站下載 Release 文件  --> https://opencv.org/releases/

2. 首先打開環境變數並且設定一些變數，此為使用者級別的環境變數

   ```terminal
   sudo vim ~/.bash_profile
   ```

3. 將以下內容貼到環境變數中

   ```terminal
   export OpenCV_HOME=/Users/zhengruihong/opencv/build
   export PATH=$PATH:$OpenCV_HOME/bin
   export QT5_HOME=/Users/zhengruihong/Qt/5.15.2/clang_64
   export PATH=$PATH:$QT5_HOME/bin
   # Mac平臺，C編譯器不在是GCC，而是clang
   export CC=clang
   export CXX=clang++
   export CXXFLAGS=-stdlib=libc++
   # 是否使用clang，如果使用的是GCC編譯，該選項應該設定為false
   export USE_CLANG=true
   # 跳過clang的一些嚴格的語法檢查，不然會將N多的警告作為Error
   export COMPILER_WARNINGS_FATAL=false
   # 連結時使用的參互式
   export LFLAGS='-Xlinker -lstdc++'
   # 使用64位資料模型
   export LP64=1
   # 告訴編譯平臺是64位，不然會按照32位來編譯
   export ARCH_DATA_MODEL=64
   # 允許自動下載依賴
   export ALLOW_DOWNLOADS=true
   # 並行編譯的執行緒數，編譯時長，爲了不影響其他工作，可以選擇2
   export HOTSPOT_BUILD_JOBS=2
   export PARALLEL_COMPILE_JOBS=2
   #ALT_PARALLEL_COMPILE_JOBS=2
   # 是否跳過與先前版本的比較
   export SKIP_COMPARE_IMAGES=true
   # 是否使用預編譯標頭檔案，加快編譯速度
   export USE_PRECOMPILED_HEADER=true
   # 是否使用增量編譯
   export INCREMENTAL_BUILD=true
   ```

4. 更新環境變數的內容

   ```terminal
   source ~/.bash_profile
   ```

   

5. 下載 Sources 文件並解壓縮，打開後放到你想放的目錄下取名叫 Opencv 等等編譯會用到

6. 在目錄下創建一個 Build 文件夾等等要用來編譯

7. 打開 CMake 放入opencv檔案位子以及build檔案位子並點選Configure

   - ###### /Users/zhengruihong/opencv-4.5.2/

   - ###### /Users/zhengruihong/opencv-4.5.2/build/

8. 選擇 unit filemake --> specify native compiler

9. ###### 選擇你的編譯器，這邊我是使用 Clang 位子放在 /usr/bin/clang++提供參考

10. 等他跑完之後會有許多選項 這邊要勾選，選擇完後按Generate

   - ###### WITH_QT

   - ###### DUILD_opencv_world

   - ###### CMAKE_INSTALL_PREFIX = /Users/zhengruihong/opencv-4.5.2/build/install

11. 選擇完後可能會跳出資料夾叫你選擇 QT5_DIR 。

    由於 OpenCV 還沒有QT6選項所以建議等到有了再改成 QT6，不然會很麻煩要改很多東西

    更改完後再按一次生成

    - ###### 這邊請選擇ＱＴ的 CMAKE 檔案位子 Qt5Config.cmake

    - ###### 我的位子在 ：/Users/zhengruihong/Qt/5.12.11/clang_64/lib/cmake/Qt5

12. cd 到剛剛創建的 build 資料夾 執行 make 等待編譯完成

    ```terminal
    cd /Users/zhengruihong/opencv-4.5.2/build && make
    ```

13. 之後 install 資料夾再 Make 一次

    ```terminal
    make install
    ```

14. 打開ＱＴ將以下兩行貼入 .Pro 檔案中

    ```.pro
    INCLUDEPATH += "/Users/USERNAME/opencv/build/install/include/opencv4"
    LIBS += -L"/Users/USERNAME/opencv/build/install/lib" -l"opencv_world"
    ```

15. 如果使用 CMake 則把以下內容貼近 CMakeLists.txt 檔案中 

    ```makefile
    cmake_minimum_required(VERSION 3.17)
    project(專案名稱)
    
    set(CMAKE_CXX_STANDARD 14)
    
    set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "/Users/zhengruihong/opencv/build/install/lib/cmake/")
    
    # Where to find CMake modules and OpenCV
    set( OpenCV_DIR "/Users/zhengruihong/opencv/build/install/" ) #改為opencv-bulid的位置
    set( CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")
    find_package( OpenCV REQUIRED )
    include_directories( ${OpenCV_INCLUDE_DIRS} )
    
    add_executable( 專案名稱 main.cpp) #當前項目名稱和cpp的名稱
    # add libs you need
    set( OpenCV_LIBS opencv_world )
    # linking
    target_link_libraries( 專案名稱 ${OpenCV_LIBS} )
    
    ```

16. 可以開始寫專案了，以下為測試用程式碼

    ```c++
    #include <opencv2/opencv.hpp>
    #include <iostream>
    
    using namespace cv;
    using namespace std;
    
    void openCapture()
    {
        VideoCapture capture(0); // 0 號攝影機
        Mat img;
    
        while(waitKey(1) < 0)
        {
            capture.read(img);
            imshow("Image", img);
        }
    }
    
    int main()
    {
        cout << "Open Capture" << endl;
        openCapture();
        cout << "Close Capture" << endl;
        return 0;
    }
    
    ```

    

參考資料：

1. ###### How to use OpenCV with with Qt MinGW on Windows Platform?

   - https://www.youtube.com/watch?v=ZOSu-2Oju-A&t=512s

2. ###### How to install OpenCV in Qt on macOS

   - https://www.youtube.com/watch?v=fcpseh9wK1Q