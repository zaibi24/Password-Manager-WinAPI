#include <windows.h>
#include <string>
#include <ctime>
#include <cstdio>
#include <cstdlib>
#include <algorithm>
#include <sstream>
#include <iomanip>

using namespace std;

/* ================= XOR ================= */
string xorCipher(string text, char key){
    for(char &c : text){
        c ^= key;
    }
    return text;
}

/* ============== HEX ENCODE / DECODE ============== */
string toHex(const string &input){
    stringstream ss;
    for(unsigned char c : input){
        ss << hex << setw(2) << setfill('0') << (int)c;
    }
    return ss.str();
}

string fromHex(const string &hex){
    string result;
    for(size_t i = 0; i < hex.length(); i += 2){
        string byte = hex.substr(i, 2);
        char chr = (char)strtol(byte.c_str(), nullptr, 16);
        result.push_back(chr);
    }
    return result;
}

/* ============== PASSWORD STRENGTH ============== */
string checkStrength(string pass){
    bool U=0,L=0,D=0,S=0;
    for(char c:pass){
        if(isupper(c)) U=1;
        else if(islower(c)) L=1;
        else if(isdigit(c)) D=1;
        else S=1;
    }
    int score = U+L+D+S+(pass.length()>=8);
    if(score<=2) return "Weak";
    if(score==3) return "Medium";
    return "Strong";
}

/* ============== PASSWORD GENERATOR ============== */
string generatePassword(int len){
    string chars="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*";
    string pass;
    for(int i=0;i<len;i++)
        pass+=chars[rand()%chars.size()];
    return pass;
}

/* ================= SAVE ================= */
void saveToFile(string site,string user,string pass){
    FILE *f=fopen("passwords.txt","a");
    if(!f) return;

    string eSite = toHex(xorCipher(site,'K'));
    string eUser = toHex(xorCipher(user,'K'));
    string ePass = toHex(xorCipher(pass,'K'));

    fprintf(f,"%s|%s|%s\n",eSite.c_str(),eUser.c_str(),ePass.c_str());
    fclose(f);
}

/* ================= SEARCH ================= */
string searchSite(string encTarget){
    FILE *f=fopen("passwords.txt","r");
    if(!f) return "No file found";

    char line[600];
    while(fgets(line,600,f)){
        string s=line;
        int p1=s.find("|"), p2=s.find("|",p1+1);

        string eSite=s.substr(0,p1);
        string eUser=s.substr(p1+1,p2-p1-1);
        string ePass=s.substr(p2+1);
        ePass.erase(remove(ePass.begin(),ePass.end(),'\n'),ePass.end());

        if(eSite==encTarget){
            fclose(f);
            return
                "Site: " + xorCipher(fromHex(eSite),'K') +
                "\nUser: " + xorCipher(fromHex(eUser),'K') +
                "\nPass: " + xorCipher(fromHex(ePass),'K');
        }
    }
    fclose(f);
    return "Not Found";
}

/* ================= GUI ================= */
HWND hSite,hUser,hPass,hSearch,hOutput;
#define BTN_SAVE 1
#define BTN_FIND 2
#define BTN_GEN  3
#define BTN_STR  4

LRESULT CALLBACK WindowProc(HWND hwnd,UINT msg,WPARAM wp,LPARAM lp){
    switch(msg){
    case WM_CREATE:
        srand(time(NULL));

        CreateWindow("STATIC","Site:",WS_CHILD|WS_VISIBLE,20,20,80,25,hwnd,0,0,0);
        hSite=CreateWindow("EDIT","",WS_CHILD|WS_VISIBLE|WS_BORDER,100,20,250,25,hwnd,0,0,0);

        CreateWindow("STATIC","User:",WS_CHILD|WS_VISIBLE,20,60,80,25,hwnd,0,0,0);
        hUser=CreateWindow("EDIT","",WS_CHILD|WS_VISIBLE|WS_BORDER,100,60,250,25,hwnd,0,0,0);

        CreateWindow("STATIC","Pass:",WS_CHILD|WS_VISIBLE,20,100,80,25,hwnd,0,0,0);
        hPass=CreateWindow("EDIT","",WS_CHILD|WS_VISIBLE|WS_BORDER,100,100,250,25,hwnd,0,0,0);

        CreateWindow("BUTTON","Save",WS_CHILD|WS_VISIBLE,20,140,80,30,hwnd,(HMENU)BTN_SAVE,0,0);
        CreateWindow("BUTTON","Strength",WS_CHILD|WS_VISIBLE,110,140,90,30,hwnd,(HMENU)BTN_STR,0,0);
        CreateWindow("BUTTON","Generate",WS_CHILD|WS_VISIBLE,210,140,140,30,hwnd,(HMENU)BTN_GEN,0,0);

        CreateWindow("STATIC","Search:",WS_CHILD|WS_VISIBLE,20,190,80,25,hwnd,0,0,0);
        hSearch=CreateWindow("EDIT","",WS_CHILD|WS_VISIBLE|WS_BORDER,100,190,250,25,hwnd,0,0,0);
        CreateWindow("BUTTON","Search",WS_CHILD|WS_VISIBLE,20,230,330,30,hwnd,(HMENU)BTN_FIND,0,0);

        hOutput=CreateWindow("EDIT","",
            WS_CHILD|WS_VISIBLE|WS_BORDER|ES_MULTILINE|ES_READONLY|
            WS_VSCROLL|ES_AUTOVSCROLL,
            20,280,740,260,hwnd,0,0,0);
        break;

    case WM_COMMAND:
        if(LOWORD(wp)==BTN_SAVE){
            char s[100],u[100],p[100];
            GetWindowText(hSite,s,100);
            GetWindowText(hUser,u,100);
            GetWindowText(hPass,p,100);
            saveToFile(s,u,p);
            SetWindowText(hOutput,"Saved Successfully!");
        }
        if(LOWORD(wp)==BTN_FIND){
            char s[100];
            GetWindowText(hSearch,s,100);
            SetWindowText(hOutput,searchSite(toHex(xorCipher(s,'K'))).c_str());
        }
        if(LOWORD(wp)==BTN_GEN){
            SetWindowText(hPass,generatePassword(12).c_str());
        }
        if(LOWORD(wp)==BTN_STR){
            char p[100];
            GetWindowText(hPass,p,100);
            SetWindowText(hOutput,("Strength: "+checkStrength(p)).c_str());
        }
        break;

    case WM_DESTROY: PostQuitMessage(0);
    }
    return DefWindowProc(hwnd,msg,wp,lp);
}

/* ================= MAIN ================= */
int WINAPI WinMain(HINSTANCE h,HINSTANCE,LPSTR,int n){
    WNDCLASS wc={};
    wc.lpfnWndProc=WindowProc;
    wc.hInstance=h;
    wc.lpszClassName="PassMgr";
    RegisterClass(&wc);

    int w=800,hg=600;
    HWND hwnd=CreateWindow("PassMgr","Password Manager (Encrypted)",
        WS_OVERLAPPEDWINDOW,
        (GetSystemMetrics(SM_CXSCREEN)-w)/2,
        (GetSystemMetrics(SM_CYSCREEN)-hg)/2,
        w,hg,0,0,h,0);

    ShowWindow(hwnd,n);
    MSG msg;
    while(GetMessage(&msg,0,0,0)){
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    return 0;  // <-- Fixed warning
}
