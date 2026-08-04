---
title: 恶魔轮盘赌
published: 2026-01-03
description: "v4.0"
image: "./cover.jpeg"
tags: ["C++"]
category: 程序
draft: false
---

```shell
-std=c++11
```
```cpp
//恶魔轮盘赌 - v4.0
#include<cstdlib>
#include<bits/stdc++.h>
#include<unistd.h>
#include<thread>
#include<cstdarg>

using namespace std;

// ================= 防乱码核心模块 =================
#ifdef _WIN32
#include <winsock2.h> 
#include <windows.h>
string U2G(const string& s){
    if(s.empty())return""; int l=MultiByteToWideChar(CP_UTF8,0,s.c_str(),-1,0,0);
    wchar_t* w=new wchar_t[l+1];MultiByteToWideChar(CP_UTF8,0,s.c_str(),-1,w,l);
    l=WideCharToMultiByte(CP_ACP,0,w,-1,0,0,0,0);char* c=new char[l+1];WideCharToMultiByte(CP_ACP,0,w,-1,c,l,0,0);
    string r(c);delete[]w;delete[]c;return r;
}string G2U(const string& s){
    if(s.empty())return""; int l=MultiByteToWideChar(CP_ACP,0,s.c_str(),-1,0,0);
    wchar_t* w=new wchar_t[l+1];MultiByteToWideChar(CP_ACP,0,s.c_str(),-1,w,l);
    l=WideCharToMultiByte(CP_UTF8,0,w,-1,0,0,0,0);char* c=new char[l+1];WideCharToMultiByte(CP_UTF8,0,w,-1,c,l,0,0);
    string r(c);delete[]w;delete[]c;return r;
}string stem(const string& f){size_t p=f.find_last_of(".");return p!=string::npos?f.substr(0,p):f;}
void AutoFixEncoding(){
    char p[MAX_PATH];GetModuleFileNameA(0,p,MAX_PATH);string cp=stem(p)+".cpp";
    string t_cpp="temp.cpp", t_exe="temp.exe"; ifstream test_temp(t_cpp);
    if(test_temp.is_open()){ string first_line; getline(test_temp, first_line); test_temp.close(); if(first_line.find("//temp") == 0){ Sleep(500); DeleteFileA(t_cpp.c_str()); DeleteFileA(t_exe.c_str()); } }
    ifstream in(cp,ios::binary);
    if(in.is_open()){ unsigned char b[3]={0}; in.read((char*)b,3);
        if(b[0]==0xEF && b[1]==0xBB && b[2]==0xBF){
            string ct((istreambuf_iterator<char>(in)),istreambuf_iterator<char>()); in.close();
            ofstream out(cp,ios::binary);out<<G2U(ct);out.close();system("chcp 65001 > nul");
            ofstream ofs(t_cpp); if(ofs.is_open()){ string m_src=cp, m_exe=p; size_t pos=0; while((pos=m_src.find("\\",pos))!=string::npos){m_src.replace(pos,1,"\\\\");pos+=2;} pos=0; while((pos=m_exe.find("\\",pos))!=string::npos){m_exe.replace(pos,1,"\\\\");pos+=2;}
                ofs<<"//temp\n#include <windows.h>\n#include <cstdlib>\n#include <string>\nint main(){\nSleep(500);\nstd::string c=\"g++ -std=c++11 \\\"\"+std::string(\""<<m_src<<"\")+\"\\\" -o \\\"\"+std::string(\""<<m_exe<<"\")+\"\\\"\";\nstd::system(c.c_str());\nstd::string r=\"start \\\"\\\" \\\"\"+std::string(\""<<m_exe<<"\")+\"\\\"\";\nstd::system(r.c_str());\nreturn 0;\n}";
                ofs.close(); system(("g++ -std=c++11 \""+t_cpp+"\" -o \""+t_exe+"\"").c_str()); system(("start \"\" \""+t_exe+"\"").c_str());
            } exit(0);
        }in.close();
    }
}
void clear_screen() { system("cls"); }
void pause_screen() { system("pause>nul"); }
#else
string U2G(const string& s){ return s; }
string G2U(const string& s){ return s; }
void clear_screen() { cout<<"\033[2J\033[H"; }
void pause_screen() { system("bash -c 'read -n 1 -s -p \"\"'"); }
#endif

void my_sleep(int ms) { std::this_thread::sleep_for(std::chrono::milliseconds(ms)); }

void put(string text) {
    string out_s = G2U(text);
    int len = out_s.size();
    for (int i = 0; i < len; i++) {
        unsigned char c = (unsigned char)out_s[i];
        putchar(c);
        
        // 修复：根据 UTF-8 编码规则判断后续字节数
        // 1110xxxx 表示 3 字节字符（中文常用），11110xxx 表示 4 字节
        if (c >= 0xF0 && i + 3 < len) {        // 4 字节字符
            putchar(out_s[++i]); putchar(out_s[++i]); putchar(out_s[++i]);
        } else if (c >= 0xE0 && i + 2 < len) { // 3 字节字符
            putchar(out_s[++i]); putchar(out_s[++i]);
        } else if (c >= 0xC0 && i + 1 < len) { // 2 字节字符
            putchar(out_s[++i]);
        }

        fflush(stdout);
        if (rand() % 2) my_sleep(15);
    }
}

void putf(const char* format, ...) {
    char buffer[4096]; va_list args;
    va_start(args, format); vsnprintf(buffer, sizeof(buffer), format, args); va_end(args);
    put(string(buffer));
}

void out(string text) { cout << G2U(text); fflush(stdout); }

int z1[1000], z2[1000], d[1000], ee[1000], xz[1000], broken;

int main() {
#ifdef _WIN32
    system("chcp 65001 > nul"); //AutoFixEncoding(); 
#endif
    srand((unsigned)time(NULL)); 
	do {
		put("恶魔轮盘赌\n"); put("按任意键继续..."); pause_screen(); clear_screen();
		int k1=0, k2=0, a=1, s1=5, s2=5, s, e, zd, p, j1=0, j2=0;
		for(int i=1;i<=5;i++){ z1[i]=0; z2[i]=0; }
		while(a!=EOF) {
			clear_screen(); s=a/3+1; if(s>3)s=3; e=a*2+1; p=0;
			putf("第%d回合\n玩家1血量：%d\n玩家2血量：%d\n伤害：%d\n", a, s1, s2, s);
			for(int i=e;i>=1;i--) {
				ee[i]=rand()%500;
				if((ee[i]%6==0)||(ee[i]%6==3)||(ee[i]%6==5)) { ee[i]=1; p++; } else ee[i]=0;
				my_sleep(500);
			}
			putf("实弹：%d\n空弹：%d\n", p, e-p);
			if(a>1) {
				put("\n玩家1获得：");
				for(int i=1;i<min(a+2+a/3,8);i++) {
					my_sleep(500); d[i]=rand()%1000;
					if((d[i]%10==2)||(d[i]%10==8)) { put("放大镜 "); d[i]=1; } else if((d[i]%10==1)||(d[i]%10==5)) { put("小刀 "); d[i]=2; }
					else if((d[i]%10==0)||(d[i]%10==7)) { put("手铐 "); d[i]=3; } else if((d[i]%10==6)||(d[i]%10==9)) { put("汽水 "); d[i]=4; } else { put("香烟 "); d[i]=5; } z1[d[i]]++;
				}
				put("\n玩家2获得：");
				for(int i=1;i<min(a+a/3+2,8);i++) {
					my_sleep(500); d[i]=rand()%500;
					if((d[i]%10==5)||(d[i]%10==7)) { put("放大镜 "); d[i]=1; } else if((d[i]%10==8)||(d[i]%10==4)) { put("小刀 "); d[i]=2; }
					else if((d[i]%10==2)||(d[i]%10==9)) { put("手铐 "); d[i]=3; } else if((d[i]%10==0)||(d[i]%10==3)) { put("汽水 "); d[i]=4; } else { put("香烟 "); d[i]=5; } z2[d[i]]++;
				}
			} put("\n按任意键继续..."); pause_screen(); clear_screen();
			
			while(e!=EOF) {
				clear_screen();
				if(k1==0) {
					put("玩家1：\n1.向对方开枪\n2.向自己开枪\n");
					if(z1[1]>0) putf("3.使用放大镜(*%d)\n", z1[1]); if(z1[2]>0) putf("4.使用小刀(*%d)\n", z1[2]); if(z1[3]>0) putf("5.使用手铐(*%d)\n", z1[3]);
					if(z1[4]>0) putf("6.使用汽水(*%d)\n", z1[4]); if(z1[5]>0) putf("7.使用香烟(*%d)\n", z1[5]);
					put("请输数字："); int x; scanf("%d",&x);
					if(x==1) {
						if(ee[e]==0) { put("空弹\n"); my_sleep(500); } else { put("实弹\n"); my_sleep(500); s2-=s; }
						putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); e--; my_sleep(500);
						if(s>=a/3+2)s=a/3+1; if(j1>0) { j1--; k2=1; } else k2=0;
					}
					if(x==2) {
						if(ee[e]==0) { put("空弹\n"); k2=1; my_sleep(500); } else { put("实弹\n"); my_sleep(500); s1-=s; if(j1>0){j1--;k2=1;}else k2=0; }
						putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); e--; my_sleep(500); if(s>=a/3+2)s=a/3+1;
					}
					if(x==3) { if(ee[e]==0) put("空弹\n"); else put("实弹\n"); my_sleep(500); k2=1; z1[1]--; }
					if(x==4) { s++; k2=1; z1[2]--; }
					if(x==5) { j1++; k2=1; z1[3]--; }
					if(x==6) { if(ee[e]==0) put("空弹\n"); else put("实弹\n"); my_sleep(500); e--; k2=1; z1[4]--; }
					if(x==7) { s1++; putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); k2=1; z1[5]--; my_sleep(500); }
					if(s2<=0) { put("\n玩家1胜利\n"); my_sleep(500); broken=1; break; }
					if(s1<=0) { put("\n玩家2胜利\n"); my_sleep(500); broken=1; break; } put("\n\n"); if(e==0)break;
				}
				if(k2==0) {
					put("玩家2：\n1.向对方开枪\n2.向自己开枪\n");
					if(z2[1]>0) putf("3.使用放大镜(*%d)\n", z2[1]); if(z2[2]>0) putf("4.使用小刀(*%d)\n", z2[2]); if(z2[3]>0) putf("5.使用手铐(*%d)\n", z2[3]);
					if(z2[4]>0) putf("6.使用汽水(*%d)\n", z2[4]); if(z2[5]>0) putf("7.使用香烟(*%d)\n", z2[5]);
					put("请输数字："); int y; scanf("%d",&y);
					if(y==1) {
						if(ee[e]==0) { put("空弹\n"); my_sleep(500); } else { put("实弹\n"); my_sleep(500); s1-=s; }
						putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); e--; my_sleep(500);
						if(s>=a/3+2)s=a/3+1; if(j2>0) { j2--; k1=1; } else k1=0;
					}
					if(y==2) {
						if(ee[e]==0) { put("空弹\n"); k1=1; my_sleep(500); } else { put("实弹\n"); my_sleep(500); s2-=s; if(j2>0){j2--;k1=1;}else k1=0; }
						putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); e--; my_sleep(500); if(s>=a/3+2)s=a/3+1;
					}
					if(y==3) { if(ee[e]==0) put("空弹\n"); else put("实弹\n"); my_sleep(500); k1=1; z2[1]--; }
					if(y==4) { s++; k1=1; z2[2]--; }
					if(y==5) { j2++; k1=1; z2[3]--; }
					if(y==6) { if(ee[e]==0) put("空弹\n"); else put("实弹\n"); my_sleep(500); e--; k1=1; z2[4]--; }
					if(y==7) { s2++; putf("\n玩家1血量：%d\n玩家2血量：%d\n", s1, s2); k1=1; z2[5]--; my_sleep(500); }
					if(s2<=0) { put("\n玩家1胜利\n"); my_sleep(500); broken=1; break; }
					if(s1<=0) { put("\n玩家2胜利\n"); my_sleep(500); broken=1; break; } put("\n\n"); if(e==0)break;
				}
			}
			if(broken==1) { broken=0; break; } a++;
		} my_sleep(500); clear_screen();
	}while(1);
	return 0; 
}
```