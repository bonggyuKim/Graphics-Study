# Direct3D

Direct3D는 마이크로소프트의 DirectX API에서 3차원 그래픽스를 담당하는 부분이다. 마소의 윈도우 운영 체제에서만 작동한다. 주로 게임 콘솔의 그래픽 API로 사용된다.

##### COM : Component Object Model(구성 요소 개체 모델)

 응용 프로그램에서 하나 이상의 작업을 수행 하는 데 사용할 수 있는 캡슐화된 기능의 구성 요소이다. COM은 레고 처럼 작동하는데 기존의 프로그램을 변경하지 않고 원하는 대로 붙이거나 분리 할 수 있다.

1.  COM 개체는 인터페이스에 의해 제어되는 클래스, 클래스 집합이다. 
2.  COM 개체의 각 유형에는 고유한 ID가 있다. 코드에서 이 ID를 사용해야 하는 경우가 있다.
3.  COM 개체 사용이 끝나면 항상 Release() 함수를 호출해서 메모리 해제를 해야 한다.
4. COM 개체는 일반적으로 'ID3D11Device'와 같이 'I'로 시작하기 때문에 식별하기 쉽다.

##### Direct3D 렌더링

 3D모델이나 텍스처와 같은 것을 렌더링해야 하는 경우 그래픽 어댑터는 이 배열을 업데이트 하고 정보를 모니터에 전송하여 표시한다. 그런 다음 모니터는 화면을 위에서 아래로 다시 그려 랜더링된 새 부분을 추가한다. 다만, 실시간 랜더링에 필요한 만큼 모니터가 빠르게 새로고침 되지 않아서 이미지가 두 개로 잘리는데 이 효과를 tearing이라고 한다. 이것을 피하기 위해 스와핑이라는 기능을 구현한다. 

 스와핑 : 새 이미지를 모니터에 직접 랜더링하는 대신 백 버퍼라는 보조 버퍼에 이미지를 그린다. 완료되면 백 버퍼와 프론트 버퍼를 교환한다. 

##### Direct3D 그래픽 파이프라인

![direct3d 11 프로그래밍 가능 파이프라인의 데이터 흐름 다이어그램](https://docs.microsoft.com/ko-kr/windows/win32/direct3d11/images/d3d11-pipeline-stages.jpg)

- Input Assember Stage : 사용자가 채운 버퍼에서 기본 데이터를 읽고 다른 파이프라인 단계에서 사용할 수 있는 기본 형식으로 데이터를 조합 하는 단계

- Vertex Shader Stage : Input Assember의 Vertex처리하여 반환, 스킨, 모핑 및 Vertex별 조명과 같은 Vertex별작업을 수행한다. 셰이더가 항상 단일 입력 Vertex에서 작동하고 단일 출력 Vertex을 생성한다. Vertex 셰이더는 파이프라인을 실행 하기 위해 항상 활성 상태여야 한다. 

- Hull Shader Stage : Tessallator 작업의 시작이 되는 단계로 폴리곤을 얼마나 세밀하게, 무슨 방법으로 분할을 시킬지 결정하는 단계

- Tessallator Stage : Hull 셰이더 단계에서 받은 Tessallator 계획에 따라 폴리곤들을 분할한다.

- Domain Shader Stage :  Tessallator 단계에서 처리되어 분할된 폴리곤들을 받아서 굴곡을 주기위해 그 구성 Vertex들의 공간 정보를 조절한다.

- Geometry Shader Stage : Mesh를 Primitive 단위로 다룰 수 있어, 삼각형, 사각형 등의 방법으로 처리 가능, 기하 셰이더의 출력을 버퍼에 전달 할 수 있는데, 전달받은 버퍼들을 통하여 폴리곤들을 읽거나 새로운 기하 구조를 생성, 또는 다른 버퍼에 저장이 가능하다. 

- Stream Output Stage : geometry-Shader(비활성 상태면 Vertex-Shader 단계)에서 메모리의 하나 이상의 버퍼로 Vertex 데이터를 지속적으로 출력하는 단계

- Rasterizer Stage : 실시간 3D 그래픽을 표시 하기 위해 벡터 정보를 래스터 이미지로 변환한다.

- Pixel Shader Stage : 렌더링 대상인 픽셀들이 화면에 어떻게 출력될지 최종적으로 결정하는 단계, 픽셀별 조명 및 후처리와 같은 음영을 넣을 수 있다.

- Output Merger Stage : 출력을 하기 위해 처리를 하는 단계, 최종적으로 파이프라인 상태, 픽셸 셰이더에서 생성된 픽셀 데이터, 렌더링 대상의 깊이, 스텐실 버퍼의 내용을 조합하여 최종 렌더링된 픽셀 색을 생성한다.

  ##### 

##### Direct3D 헤더

```
// 기본 윈도우 헤더 파일  과 Direct3D 헤더 파일을 포함한다

include <windows.h>

# include <windowsx.h>

# include <d3d11.h>

# include <d3dx11.h>

# include <d3dx10.h>

 // Direct3D 라이브러리 파일 포함 

#pragma comment(lib, "d3d11.lib")

#pragma comment(lib, "d3dx11.lib")

#pragma comment(lib, "d3dx10.lib")

// 전역 선언 
IDXGISwapChain *swapchain; // 스왑 체인 인터페이스에 대한 포인터 
ID3D11Device *dev; // Direct3D 장치 인터페이스에 대한 포인터 
ID3D11DeviceContext *devcon; // Direct3D 장치 컨텍스트에 대한 포인터 

// 함수 프로토타입
무효 InitD3D(HWND hWnd); // Direct3D 설정 및 초기화 
void CleanD3D(void); // Direct3D를 닫고 메모리를 해제합니다.

 // WindowProc 함수 프로토타입 
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);
```

###### include<d3dx11.h>

 여기에는 Direct3D 11헤더 파일이 포함된다.

 d3dx11.h파일에는 Direct3D의 핵심 부분이 포함되어 있고, 게임이나 기타 그래픽 프로그램을 작성할 때 매우 유용할 수 있다.

###### include<d3dx10.h>

 Direct3D 11은 Direct3D 10의 확장이므로 10에서 많은 매크로, 함수 및 클래스를 차용한다.

###### pragma comment(lib, "d3d11.lib")

 Direct3D 11 라이브러리 파일이 포함된다. pragma comment 지시문은 프로젝트의 개체 파일에 특정 정보를 배치한다. d3d11.lib 파일을 추가.

###### ID3D11Device *dev

 ID3D11Device라는 COM 개체를 생성한다. COM이 이 개체를 만들 때 이 포인터를 사용하여 간접적으로 액세스한다.

###### ID3D11DeviceContext *devcon

 GPU 및 렌더링 파이프라인관리를 담당한다.

##### DIrect3D 실행

```
// 이 함수는 사용을 위해 Direct3D를 초기화하고 준비합니다. 
void InitD3D(HWND hWnd) 
{ 
    // 스왑 체인에 대한 정보를 저장할 구조체를 만듭니다 
    . DXGI_SWAP_CHAIN_DESC scd; 

    // 사용할 구조체를 지웁니다. 
    ZeroMemory(&scd, sizeof(DXGI_SWAP_CHAIN_DESC)); 

    // 스왑 체인 설명 채우기 struct 
    scd.BufferCount = 1; // 백 버퍼 하나 
    scd.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM; // 32비트 색상 사용 
    scd.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT; // 스왑 체인을 사용하는 방법 
    scd.OutputWindow = hWnd; // 사용할 창
    scd.SampleDesc.Count = 4; // 멀티샘플 수 
    scd.Windowed = TRUE; // 창/전체 화면 모드 

    // scd struct 
    D3D11CreateDeviceAndSwapChain(NULL, 
                                  D3D_DRIVER_TYPE_HARDWARE, 
                                  NULL, 
                                  NULL, 
                                  NULL, 
                                  NULL, 
                                  D3D11_SDK_VERSION, 
                                  &scd, 
                                  &swapchain,
                                  &dev, 
                                  NULL, 
                                  &devcon); 
}
```

###### scd.SampleDesc.Count = 4

 Direct3D에 MSAA(다중 샘플 안티 앨리어싱) 렌더링을 수행하는 방법을 알려주는데 사용된다.

 안티 앨리어싱은 각 픽셀을 주변 픽셀과 약간 혼합하여 모양의 가장자리를 부드럽게 랜더링 해준다. 숫자가 높을수록 좋아지고 최대 4까지 지원한다.

###### D3D11CreateDeviceAndSwapChain()

Device, Device 컨텍스트 및 스왑 체인 COM 개체를 만든다.

```
HRESULT D3D11CreateDeviceAndSwapChain ( 
    IDXGIAdapter * pAdapter, 
    D3D_DRIVER_TYPE DriverType, 
    HMODULE Software, 
    UINT Flag, 
    D3D_FEATURE_LEVEL * pFeatureLevels, 
    UINT FeatureLevels, 
    UINT SDKVersion, 
    DXGI_SWAP_CHAIN_DESC * pSwapChainDesc, 
    IDXGISwapChain ** ppSwapChain, 
    ID3D11Device ** ppDevice, 
    D3D_FEATURE_LEVEL * pFeatureLevel, 
    ID3D11DeviceContext ** ppDeviceContext);
```

###### IDXGIAdapter *pAdapter

 Direct3D가 사용해야 하는 그래픽 어댑터를 나타내는 값, 그래픽 어댑터는 GPU 및 해당 비디오 메모리, 디지털 - 아날로그 변환기 등을 나타낸다. NULL을 사용하여 DXGI가 알아서 처리하도록 한다.

###### D3D_DRIVER_TYPE Driver Type

 Direct3D가 렌더링에 하드웨어 또는 소프트웨어를 사용해야 하는지 여부를 결정하는 데 사용된다.

여기서 사용된 HARDWARE는 GPU 하드웨어을 사용한다(그래픽카드).

###### HMODULE Software

 다루지 않는다.

###### Unit Flag

 Direct3D가 실행되는 방식을 변경할 수 있는 Flag. 여기서는 쓰지 않는다.

...

생략

##### Closing Direct3D 

```
// Direct3D 및 COM을 정리하는 함수입니다.
void CleanD3D()
{
    // 기존 COM 개체를 모두 닫고 해제합니다.
    swapchain->Release();
    dev->Release();
    devcon->Release();
}
```

##### Main cpp

```
// include the basic windows header files and the Direct3D header files
#include <windows.h>
#include <windowsx.h>
#include <d3d11.h>
#include <d3dx11.h>
#include <d3dx10.h>

// include the Direct3D Library file
#pragma comment (lib, "d3d11.lib")
#pragma comment (lib, "d3dx11.lib")
#pragma comment (lib, "d3dx10.lib")

// global declarations
IDXGISwapChain *swapchain;             // the pointer to the swap chain interface
ID3D11Device *dev;                     // the pointer to our Direct3D device interface
ID3D11DeviceContext *devcon;           // the pointer to our Direct3D device context

// function prototypes
void InitD3D(HWND hWnd);    // sets up and initializes Direct3D
void CleanD3D(void);        // closes Direct3D and releases memory

// the WindowProc function prototype
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);


// the entry point for any Windows program
int WINAPI WinMain(HINSTANCE hInstance,
                   HINSTANCE hPrevInstance,
                   LPSTR lpCmdLine,
                   int nCmdShow)
{
    HWND hWnd;
    WNDCLASSEX wc;

    ZeroMemory(&wc, sizeof(WNDCLASSEX));

    wc.cbSize = sizeof(WNDCLASSEX);
    wc.style = CS_HREDRAW | CS_VREDRAW;
    wc.lpfnWndProc = WindowProc;
    wc.hInstance = hInstance;
    wc.hCursor = LoadCursor(NULL, IDC_ARROW);
    wc.hbrBackground = (HBRUSH)COLOR_WINDOW;
    wc.lpszClassName = L"WindowClass";

    RegisterClassEx(&wc);

    RECT wr = {0, 0, 800, 600};
    AdjustWindowRect(&wr, WS_OVERLAPPEDWINDOW, FALSE);

    hWnd = CreateWindowEx(NULL,
                          L"WindowClass",
                          L"Our First Direct3D Program",
                          WS_OVERLAPPEDWINDOW,
                          300,
                          300,
                          wr.right - wr.left,
                          wr.bottom - wr.top,
                          NULL,
                          NULL,
                          hInstance,
                          NULL);

    ShowWindow(hWnd, nCmdShow);

    // set up and initialize Direct3D
    InitD3D(hWnd);

    // enter the main loop:

    MSG msg;

    while(TRUE)
    {
        if(PeekMessage(&msg, NULL, 0, 0, PM_REMOVE))
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);

            if(msg.message == WM_QUIT)
                break;
        }
        else
        {
            // Run game code here
            // ...
            // ...
        }
    }

    // clean up DirectX and COM
    CleanD3D();

    return msg.wParam;
}


// this is the main message handler for the program
LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch(message)
    {
        case WM_DESTROY:
            {
                PostQuitMessage(0);
                return 0;
            } break;
    }

    return DefWindowProc (hWnd, message, wParam, lParam);
}


// this function initializes and prepares Direct3D for use
void InitD3D(HWND hWnd)
{
    // create a struct to hold information about the swap chain
    DXGI_SWAP_CHAIN_DESC scd;

    // clear out the struct for use
    ZeroMemory(&scd, sizeof(DXGI_SWAP_CHAIN_DESC));

    // fill the swap chain description struct
    scd.BufferCount = 1;                                    // one back buffer
    scd.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM;     // use 32-bit color
    scd.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;      // how swap chain is to be used
    scd.OutputWindow = hWnd;                                // the window to be used
    scd.SampleDesc.Count = 4;                               // how many multisamples
    scd.Windowed = TRUE;                                    // windowed/full-screen mode

    // create a device, device context and swap chain using the information in the scd struct
    D3D11CreateDeviceAndSwapChain(NULL,
                                  D3D_DRIVER_TYPE_HARDWARE,
                                  NULL,
                                  NULL,
                                  NULL,
                                  NULL,
                                  D3D11_SDK_VERSION,
                                  &scd,
                                  &swapchain,
                                  &dev,
                                  NULL,
                                  &devcon);
}


// this is the function that cleans up Direct3D and COM
void CleanD3D(void)
{
    // close and release all existing COM objects
    swapchain->Release();
    dev->Release();
    devcon->Release();
}
```

