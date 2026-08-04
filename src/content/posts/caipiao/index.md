---
title: 阎帝彩票
published: 2026-01-02
description: "v7.0"
image: "./cover.jpeg"
tags: ["C++"]
category: 程序
draft: false
---

```shell
-std=c++11
```
```cpp
//阎帝彩票 - v7.0
#include <bits/stdc++.h>
#include <thread>
#include <chrono>
#include <fstream> 
#include <sstream>
#include <cstdarg>

using namespace std;

// ================= 防乱码与输出核心模块 =================
#ifdef _WIN32
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
    string t_cpp="temp.cpp", t_exe="temp.exe";
    ifstream test_temp(t_cpp);
    if(test_temp.is_open()){
        string first_line; getline(test_temp, first_line); test_temp.close();
        if(first_line.find("//temp") == 0){ Sleep(500); DeleteFileA(t_cpp.c_str()); DeleteFileA(t_exe.c_str()); }
    }
    ifstream in(cp,ios::binary);
    if(in.is_open()){
        unsigned char b[3]={0}; in.read((char*)b,3);
        if(b[0]==0xEF && b[1]==0xBB && b[2]==0xBF){
            string ct((istreambuf_iterator<char>(in)),istreambuf_iterator<char>()); in.close();
            ofstream out(cp,ios::binary);out<<G2U(ct);out.close();system("chcp 65001 > nul");
            ofstream ofs(t_cpp);
            if(ofs.is_open()){
                string m_src=cp, m_exe=p; size_t pos=0; 
                while((pos=m_src.find("\\",pos))!=string::npos){m_src.replace(pos,1,"\\\\");pos+=2;} pos=0; 
                while((pos=m_exe.find("\\",pos))!=string::npos){m_exe.replace(pos,1,"\\\\");pos+=2;}
                ofs<<"//temp\n#include <windows.h>\n#include <cstdlib>\n#include <string>\nint main(){\nSleep(500);\nstd::string c=\"g++ -std=c++11 \\\"\"+std::string(\""<<m_src<<"\")+\"\\\" -o \\\"\"+std::string(\""<<m_exe<<"\")+\"\\\"\";\nstd::system(c.c_str());\nstd::string r=\"start \\\"\\\" \\\"\"+std::string(\""<<m_exe<<"\")+\"\\\"\";\nstd::system(r.c_str());\nreturn 0;\n}";
                ofs.close(); system(("g++ -std=c++11 \""+t_cpp+"\" -o \""+t_exe+"\"").c_str()); system(("start \"\" \""+t_exe+"\"").c_str());
            } exit(0);
        }in.close();
    }
}
#else
#include <unistd.h>
#include <sys/stat.h> 
string U2G(const string& s){ return s; }
string G2U(const string& s){ return s; }
#endif

void my_sleep(int ms) { std::this_thread::sleep_for(std::chrono::milliseconds(ms)); }

void put(string text) {
    string out_s = G2U(text);
    int len = out_s.size();
    for (int i = 0; i < len; i++) {
        unsigned char c = (unsigned char)out_s[i];
        putchar(c);
        if (c >= 0xF0 && i + 3 < len) { putchar(out_s[++i]); putchar(out_s[++i]); putchar(out_s[++i]); } 
        else if (c >= 0xE0 && i + 2 < len) { putchar(out_s[++i]); putchar(out_s[++i]); } 
        else if (c >= 0xC0 && i + 1 < len) { putchar(out_s[++i]); }
        fflush(stdout); if (rand() % 2) my_sleep(15);
    }
}

void putf(const char* format, ...) {
    char buffer[4096]; va_list args;
    va_start(args, format); vsnprintf(buffer, sizeof(buffer), format, args); va_end(args);
    put(string(buffer));
}

void out(string text) { cout << G2U(text); fflush(stdout); }
void outf(const char* format, ...) {
    char buffer[4096]; va_list args;
    va_start(args, format); vsnprintf(buffer, sizeof(buffer), format, args); va_end(args);
    out(string(buffer));
}

#define SAVE_FILE ".yandi_save.dat" 

void clear_screen() {
#ifdef _WIN32
    system("cls");
#else
    out("\033[2J\033[H");
#endif
}

void HideCursor() {
#ifdef _WIN32
    CONSOLE_CURSOR_INFO cursor_info = {1, 0};
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &cursor_info);
#else
    out("\033[?25l"); 
#endif
}

void set_title(const string& title) {
#ifdef _WIN32
    system(("title " + title).c_str());
#else
    out("\033]0;" + title + "\007"); 
#endif
}

void pause_screen() {
#ifdef _WIN32
    system("pause>nul");
#else
    system("bash -c 'read -n 1 -s -p \"\"'"); 
#endif
}

void trim_string(string &s) {
    if (!s.empty() && s.back() == '\r') s.pop_back();
    if (!s.empty() && s.back() == '\n') s.pop_back();
}

// ===============================================

string ye = "100";
bool auto_save = false; 
bool xysz[110]; bool f[4][8];
int sm[10], shuzi[20], cp6[20];

// ================= String 资产高精度运算 =================
int cmp(string a, string b) {
    int p_a = 0; while(p_a < (int)a.length() - 1 && a[p_a] == '0') p_a++;
    int p_b = 0; while(p_b < (int)b.length() - 1 && b[p_b] == '0') p_b++;
    string a_clean = a.substr(p_a), b_clean = b.substr(p_b);
    if(a_clean.length() != b_clean.length()) return a_clean.length() > b_clean.length() ? 1 : -1;
    for(size_t i=0; i<a_clean.length(); i++) { if(a_clean[i] != b_clean[i]) return a_clean[i] > b_clean[i] ? 1 : -1; }
    return 0;
}
string add(string a, string b) {
    if (a.empty()) a = "0"; if (b.empty()) b = "0";
    string res = ""; int carry = 0, i = a.length() - 1, j = b.length() - 1;
    while(i >= 0 || j >= 0 || carry) {
        int sum = carry; if(i >= 0) sum += a[i--] - '0'; if(j >= 0) sum += b[j--] - '0';
        res += to_string(sum % 10); carry = sum / 10;
    } reverse(res.begin(), res.end()); return res;
}
string sub(string a, string b) {
    if (a.empty()) a = "0"; if (b.empty()) b = "0"; if (cmp(a, b) <= 0) return "0";
    string res = ""; int borrow = 0, i = a.length() - 1, j = b.length() - 1;
    while(i >= 0) {
        int diff = (a[i] - '0') - borrow; if(j >= 0) diff -= (b[j] - '0');
        if(diff < 0) { diff += 10; borrow = 1; } else borrow = 0; res += to_string(diff); i--; j--;
    }
    while(res.length() > 1 && res.back() == '0') res.pop_back(); reverse(res.begin(), res.end()); return res;
}
string mul(string a, int b) {
    if (a == "0" || b == 0) return "0"; string res = ""; long long carry = 0;
    for (int i = (int)a.length() - 1; i >= 0; i--) {
        long long cur = (a[i] - '0') * 1LL * b + carry; res += to_string(cur % 10); carry = cur / 10;
    }
    while (carry) { res += to_string(carry % 10); carry /= 10; } reverse(res.begin(), res.end()); return res;
}
double string_to_double(string s) {
    if (s.length() < 15) return stod(s);
    string prefix = s.substr(0, 10); double val = stod(prefix); return val * pow(10, (int)s.length() - 10);
}
int calc_hash(const string& s) {
    int h = 0; for(char c : s) h = (1LL * h * 31 + c) % 1000000007; return h ^ 0x99;
}

// ================= 开发者模式 =================
double dev_rate_wuqiong = 0.3; int dev_rate_chuantong_fail = 35; 
double dev_rate_xingyun_mult = 1.0; double dev_rate_guaguale_mult = 1.0; 
double dev_rate_danseqiu_mult = 1.0; double dev_rate_daxiao_mult = 1.0; double dev_rate_danzhu_mult = 1.0; 
bool isk; 
void dev_mode() {
    isk=1;
    while(1) {
        clear_screen();
        put("========== 开发者模式 ==========\n");
        putf("1. 无穷彩票中奖率 (当前: %.2f%%)\n2. 传统彩票不中概率 (当前: %d%%)\n3. 幸运数字奖金倍率 (当前: %.2fx)\n4. 刮刮乐奖金倍率 (当前: %.2fx)\n5. 单色球奖金倍率 (当前: %.2fx)\n6. 买大小奖金倍率 (当前: %.2fx)\n7. 弹珠游戏收益倍率 (当前: %.2fx)\n0. 返回\n\n请选择要调整的选项:\n",
            dev_rate_wuqiong*100.0, dev_rate_chuantong_fail, dev_rate_xingyun_mult, dev_rate_guaguale_mult, dev_rate_danseqiu_mult, dev_rate_daxiao_mult, dev_rate_danzhu_mult);
        
        string sel; getline(cin, sel); trim_string(sel);
        if(sel == "1") { put("请输入新的中奖率(0.0 - 100.0):\n"); double val; if(cin >> val) { dev_rate_wuqiong = max(0.0, min(1.0, val / 100.0)); } cin.clear(); cin.ignore(10000, '\n'); }
        else if(sel == "2") { put("请输入新的不中概率(0 - 100):\n"); int val; if(cin >> val) { dev_rate_chuantong_fail = max(0, min(100, val)); } cin.clear(); cin.ignore(10000, '\n'); }
        else if(sel >= "3" && sel <= "7") {
            put("请输入新的奖金倍率:\n"); double val; if(cin >> val) { 
                if(sel=="3") dev_rate_xingyun_mult=max(0.0,val); if(sel=="4") dev_rate_guaguale_mult=max(0.0,val);
                if(sel=="5") dev_rate_danseqiu_mult=max(0.0,val); if(sel=="6") dev_rate_daxiao_mult=max(0.0,val); if(sel=="7") dev_rate_danzhu_mult=max(0.0,val);
            } cin.clear(); cin.ignore(10000, '\n');
        } else break;
    }
}

// ================= 存档模块 =================
string xor_encrypt(const string& data) { string result = data; char key = 0x5A; for (size_t i = 0; i < result.length(); ++i) result[i] ^= key; return result; }
void unlock_file() {
#ifdef _WIN32
    SetFileAttributesA(SAVE_FILE, FILE_ATTRIBUTE_NORMAL);
#else
    chmod(SAVE_FILE, S_IRUSR | S_IWUSR); 
#endif
}
void lock_file() {
#ifdef _WIN32
    SetFileAttributesA(SAVE_FILE, FILE_ATTRIBUTE_HIDDEN | FILE_ATTRIBUTE_READONLY | FILE_ATTRIBUTE_SYSTEM);
#else
    chmod(SAVE_FILE, S_IRUSR); 
#endif
}
void load_game() {
    unlock_file(); ifstream in(SAVE_FILE, ios::binary); bool isl=0;
    if(in.is_open()){
        put("检测到历史存档配置，是否加载？（1/0）\n"); cin>>isl; cin.ignore(); 
        if(isl) {
            stringstream buffer; buffer << in.rdbuf(); string decrypted = xor_encrypt(buffer.str());
            string loaded_ye; int hash_check; bool loaded_save; stringstream parser(decrypted);
            if (parser >> loaded_ye >> loaded_save >> hash_check) {
                bool ok = false; if (hash_check == calc_hash(loaded_ye)) ok = true;
                else { try { int old_ye = stoi(loaded_ye); if ((old_ye ^ 0x99) == hash_check) ok = true; } catch (...) {} }
                if (ok) { ye = loaded_ye; auto_save = loaded_save; put("存档加载成功！\n"); my_sleep(500); } 
                else { put("警告：检测到存档文件被非法篡改！资金重置为100。\n"); my_sleep(1500); ye = "100"; auto_save = false; }
            } else { put("存档已损坏，初始化默认资金。\n"); my_sleep(1000); ye = "100"; auto_save = false; }
        }
        in.close();
    } else { ye = "100"; auto_save = false; }
    lock_file(); 
}
void save_game() {
    if(auto_save) { unlock_file(); ofstream out(SAVE_FILE, ios::binary); int hash_check = calc_hash(ye); stringstream ss; ss << ye << " " << auto_save << " " << hash_check; out << xor_encrypt(ss.str()); out.close(); lock_file(); }
}

void gotoxy(short x, short y) {
#ifdef _WIN32
    COORD pos = {x, y}; SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), pos);
#else
    outf("\033[%d;%dH", y + 1, x + 1);
#endif
}

int shouyi[51], pos[1100], ans=0, n;
char s[14][51]={
	"                                                  ",
	"                       *   *                      ",
	"                     *   *   *                    ",
	"                   *   *   *   *                  ",
	"                 *   *   *   *   *                ",
	"               *   *   *   *   *   *              ",
	"             *   *   *   *   *   *   *            ",
	"           *   *   *   *   *   *   *   *          ",
	"         *   *   *   *   *   *   *   *   *        ",
	"       *   *   *   *   *   *   *   *   *   *      ",
	"     *   *   *   *   *   *   *   *   *   *   *    ",
	"   *   *   *   *   *   *   *   *   *   *   *   *  ",
	" *   *   *   *   *   *   *   *   *   *   *   *   *",
	" |999|888| 88| 15| 6 | 8 | 8 | 6 | 15| 88|888|999|",
};
inline void init(){
	shouyi[3]=999; shouyi[7]=888; shouyi[11]=88; shouyi[15]=15; shouyi[19]=6; shouyi[23]=8; shouyi[27]=8; shouyi[31]=6; shouyi[35]=15; shouyi[39]=88; shouyi[43]=888; shouyi[47]=999;
}
void yandi() {
    clear_screen(); put("-----作弊者模式-----\n过量增加余额会使游戏失去乐趣！\n增加多少?\n");
    string jiba; cin >> jiba; cin.ignore(); ye = add(ye, jiba);
    put("·"); my_sleep(50); put("·"); my_sleep(50); put("·"); my_sleep(50); put("·"); my_sleep(50); put("·"); my_sleep(50); put("\n成功!\n"); my_sleep(1000);
}

int main(int argc, char* argv[]) {
#ifdef _WIN32
    system("chcp 65001 > nul"); //AutoFixEncoding(); 
#endif

	set_title("阎帝彩票"); init(); srand(time(0)); load_game(); 
    while(1) {
        save_game(); if(ye == "" || ye[0] == '-') ye = "0"; HideCursor(); clear_screen();
        putf("余额:%s\n1:购买彩票\n2:查看规则\n3:%s\n4:清除本地记录\n0:关于\n", ye.c_str(), auto_save ? "关闭本地保存" : "开启本地保存");
        string cz; getline(cin,cz); trim_string(cz); if(cz=="") cz="1";
        
        if(cz=="7891") yandi();
        if(cz=="0") {
            while(1) {
                clear_screen();
                putf("----------关于游戏----------\n1.游戏名称：阎帝彩票\n2.余额：%s\n3.本地存档状态：%s\n4.版本号：7.0\n", ye.c_str(), auto_save ? "yes" : "no");
                if(isk) put("9.开发者模式\n"); put("0.返回主页\n");
                string sub; getline(cin, sub); trim_string(sub); if(sub == "9") dev_mode(); else break;
            } continue;
        }
        if(cz=="3") { auto_save = !auto_save; save_game(); put(string("\n[系统] 已") + (auto_save?"开启":"关闭") + "本地保存！\n"); my_sleep(1500); continue; }
        if(cz=="4") { auto_save = false; unlock_file(); remove(SAVE_FILE); put("\n[系统] 本地隐秘记录已彻底粉碎并清除！\n"); my_sleep(1500); continue; }

        if(cz=="1") {
    		HideCursor(); clear_screen();
            put("1.无穷彩票(刺激)\n2.传统彩票\n3.幸运数字\n4.刮刮乐\n5.单色球\n6.买大小(点数)\n7.弹珠游戏\n0.退出\n");
            int cpzl; scanf("%d",&cpzl); cin.ignore(); 
            if(cpzl==1) {
                clear_screen(); putf("余额：%s\n无穷彩票(10元/张)\n买几张彩票？\n", ye.c_str()); int gs;scanf("%d",&gs); cin.ignore(); if(gs==0)continue;
                string cost = to_string(10LL * gs); if(cmp(cost, ye) > 0){put("余额不足…\n");my_sleep(1000);continue;}
                ye = sub(ye, cost); string sumcp = "0"; long long batch_sum = 0;
                std::mt19937_64 gen(std::random_device{}()); std::uniform_int_distribution<long long> dist(0, 1000000000LL);

                for(int i=1; i<=gs; i++){
                    long long win = 0;
                    if((rand()%10000)/10000.0 < dev_rate_wuqiong) {
                        win = dist(gen);
                        if(win > 10000000 && rand() % 100000 < 99995) win = (rand() % 200000 + 1) * 5; 
                        if(win > 1000000  && rand() % 10000 < 9995) win = (rand() % 20000 + 1) * 5;   
                        if(win > 100000   && rand() % 1000 < 995) win = (rand() % 2000 + 1) * 5;     
                        if(win > 10000    && rand() % 100 < 98) win = (rand() % 200 + 1) * 5;       
                        if(win > 1000     && rand() % 100 < 95) win = (rand() % 10 + 1) * 5;       
                        if(win > 50       && rand() % 100 < 80) win = (rand() % 3 + 1) * 5;       
                        if(win > 10       && rand() % 100 <= (100-dev_rate_wuqiong*100)) win = (rand() % 3) * 5;       
                    }
                    batch_sum += win; if (batch_sum > 1000000000000000LL) { sumcp = add(sumcp, to_string(batch_sum)); batch_sum = 0; }
                    if(win >= 10000) put("!!!"); printf("第%d张中了%lld元！\n", i, win);
                    if(gs<=50) my_sleep(100); else if(gs<=200) my_sleep(10); else if(gs<=1000) my_sleep(5); else{}
                }
                sumcp = add(sumcp, to_string(batch_sum)); ye = add(ye, sumcp);
                putf("\n总共中了%s元！\n平均每张%lf元\npress enter to continue…\n", sumcp.c_str(), string_to_double(sumcp)/gs); getchar();
            } 
            else if(cpzl==2) {
    			HideCursor(); clear_screen(); putf("余额：%s\n传统彩票(10元/张)\n买几张彩票？\n", ye.c_str()); int gs;scanf("%d",&gs); cin.ignore(); if(gs==0)continue;
                string cost = to_string(10LL * gs); if(cmp(cost, ye) > 0){put("余额不足…\n");my_sleep(1000);continue;}
                ye = sub(ye, cost); long long sumcp=0; int fail_rate = dev_rate_chuantong_fail; int left = 100 - fail_rate; if(left == 0) left = 1; 
                int v1 = fail_rate + left * 40 / 65; int v2 = v1 + left * 15 / 65;
                for(int i=1;i<=gs;i++){
                    int sj=rand()%100;
                    if(sj < fail_rate) putf("第%d张中0元\n",i); else if(sj < v1) {putf("第%d张中10元\n",i); sumcp+=1;}
                    else if(sj < v2) {putf("第%d张中20元\n",i); sumcp+=2;} else {putf("第%d张中50元\n",i); sumcp+=5;}
					if(gs>=1000)my_sleep(0); else if(gs>=100)my_sleep(20); else if(gs>=20)my_sleep(50); else my_sleep(500);
                }
                ye = add(ye, to_string(sumcp * 10LL)); putf("总共中了%lld元！\n平均每张%lf元\npress enter to continue…\n",sumcp*10, 1.0*sumcp*10/gs); getchar();
            }
            else if(cpzl==3) {
    			HideCursor(); clear_screen(); putf("余额：%s\n幸运数字(20元/张)\n可以选择:\n", ye.c_str()); memset(xysz,0,sizeof(xysz));
                for(int i=1;i<=100;i++){ int sj=rand()%3; if(!sj)xysz[i]=1,putf("%d ",i); }
                put("\n"); int dajiang=rand()%100+1; my_sleep(500); put("买几张?\n"); int gs;scanf("%d",&gs); if(gs==0) { cin.ignore(); continue; }
                string cost = to_string(20LL * gs); if(cmp(cost, ye) > 0){put("余额不足…\n");my_sleep(1000); cin.ignore(); continue;}
                ye = sub(ye, cost); put("请选择(空格隔开):"); bool bk=0; long long sumcp=0;
                for(int i=1,x;i<=gs;i++){
                    scanf("%d",&x);
                    if(!xysz[x]){put("所选数字不在范围内，请重新输入\n");i--;}
                    else{
                        xysz[x]=1; int win_amount = 0;
                        if(abs(x-dajiang)<=0) win_amount = 150 * dev_rate_xingyun_mult; else if(abs(x-dajiang)<=3) win_amount = 80 * dev_rate_xingyun_mult;
                        else if(abs(x-dajiang)<=5) win_amount = 40 * dev_rate_xingyun_mult; else if(abs(x-dajiang)<=10) win_amount = 20 * dev_rate_xingyun_mult; else if(abs(x-dajiang)<=20) win_amount = 10 * dev_rate_xingyun_mult;
                        if(win_amount > 0) { putf("中%d元!\n", win_amount); sumcp += win_amount; } else put("没中…\n");
                    }
                }
                cin.ignore(); if(bk)continue; my_sleep(500); put("大奖数字是…"); my_sleep(1000); putf("%d!\n",dajiang); my_sleep(1000);
                ye = add(ye, to_string(sumcp)); putf("总共中了%lld元！\n平均每张%lf元\npress enter to continue…\n",sumcp,1.0*sumcp/gs); getchar();
            }
            else if(cpzl==4) {
    			HideCursor(); clear_screen(); putf("余额：%s\n--刮刮乐--\n1.10元\n2.20元\n3.30元\n4.50元\n0.退出\n选择种类:\n", ye.c_str());
                int zl;scanf("%d",&zl); cin.ignore(); if(zl==0)continue;
                if((zl==1&&cmp(ye,"10")<0)||(zl==2&&cmp(ye,"20")<0)||(zl==3&&cmp(ye,"30")<0)||(zl==4&&cmp(ye,"50")<0)){put("余额不足…\n");my_sleep(1000);continue;}
                clear_screen(); int q;
                if(zl==1){q=rand()%21 + 5; ye=sub(ye,"10");} else if(zl==2){q=rand()%41 + 10; ye=sub(ye,"20");}  
                else if(zl==3){q=rand()%61 + 15; ye=sub(ye,"30");} else if(zl==4){q=rand()%101 + 25; ye=sub(ye,"50");} 
                long long sumcp = q * dev_rate_guaguale_mult; int len=0; memset(sm,-1,sizeof(sm)); int tmp_q = q; 
                while(tmp_q)sm[++len]=tmp_q%10,tmp_q/=10; memset(f,0,sizeof(f)); if(len==0)sm[1]=0;len=3;
                for(int i=len,x;i>=1;i--){
                    x=sm[i]; if(x==1)f[len-i][3]=f[len-i][6]=1; if(x==2)f[len-i][3]=f[len-i][1]=f[len-i][4]=f[len-i][5]=f[len-i][7]=1;
                    if(x==3)f[len-i][3]=f[len-i][1]=f[len-i][4]=f[len-i][6]=f[len-i][7]=1; if(x==4)f[len-i][3]=f[len-i][2]=f[len-i][4]=f[len-i][6]=1;
                    if(x==5)f[len-i][1]=f[len-i][2]=f[len-i][4]=f[len-i][6]=f[len-i][7]=1; if(x==6)f[len-i][1]=f[len-i][2]=f[len-i][4]=f[len-i][6]=f[len-i][7]=f[len-i][5]=1;
                    if(x==7)f[len-i][1]=f[len-i][3]=f[len-i][6]=1; if(x==8)f[len-i][1]=f[len-i][2]=f[len-i][3]=f[len-i][4]=f[len-i][5]=f[len-i][6]=f[len-i][7]=1;
                    if(x==9)f[len-i][1]=f[len-i][2]=f[len-i][3]=f[len-i][4]=f[len-i][6]=f[len-i][7]=1; if(x==0)f[len-i][1]=f[len-i][2]=f[len-i][3]=f[len-i][5]=f[len-i][6]=f[len-i][7]=1;
                }
                for(int i=0;i<=31;i++){
                    gotoxy(0,0); out("--------------------------------\n");
                    out("|"); for(int j=1;j<=i;j++){if(f[j/10][1]&&j%10>=2&&j%10<=7)out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if((f[j/10][2]&&j%10==1)||(f[j/10][3]&&j%10==8))out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if((f[j/10][2]&&j%10==1)||(f[j/10][3]&&j%10==8))out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if(f[j/10][4]&&j%10>=2&&j%10<=7)out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if((f[j/10][5]&&j%10==1)||(f[j/10][6]&&j%10==8))out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if((f[j/10][5]&&j%10==1)||(f[j/10][6]&&j%10==8))out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("|"); for(int j=1;j<=i;j++){if(f[j/10][7]&&j%10>=2&&j%10<=7)out("*");else out(" ");} for(int j=i;j<=30;j++)out("?");out("|\n");
                    out("--------------------------------\n"); pause_screen(); 
                }
                ye = add(ye, to_string(sumcp)); putf("恭喜！刮出了%lld元！(含倍率加成)\npress enter to continue…\n",sumcp); getchar();
            }
            else if(cpzl==5) {
    			HideCursor(); clear_screen(); long long sumcp=0; putf("余额：%s\n单色球(10元/张)\n买几张？\n", ye.c_str());
                int gs;scanf("%d",&gs); if(gs==0) { cin.ignore(); continue; }
                string cost = to_string(10LL * gs); if(cmp(cost, ye) > 0){put("余额不足…\n");my_sleep(1000); cin.ignore(); continue;}
                ye = sub(ye, cost); clear_screen(); put("请输入6个你猜的数字(1~16之间，空格隔开)\n"); memset(shuzi,0,sizeof(shuzi));
                while(gs--)for(int i=1,x;i<=6;i++)scanf("%d",&x),shuzi[x]++; cin.ignore();
                for(int i=1,x;i<=6;i++){
                    x=rand()%16+1; putf("第%d个开奖球是...",i); my_sleep(1000);
                    putf("%d!\n",x); sumcp += shuzi[x] * 10 * dev_rate_danseqiu_mult; my_sleep(1000);
                }
                ye = add(ye, to_string(sumcp)); putf("中了%lld元！\npress enter to continue…\n",sumcp); getchar();
            }
            else if(cpzl==6) {
    			HideCursor(); clear_screen(); putf("余额：%s\n", ye.c_str());
            	out("_______________________________________________________\n|                          |                          |\n|        1.小（1~3）       |        2.大（4~6）       |\n|__________________________|__________________________|\n|                 |                 |                 |\n|   3.小（1~2）   |   4.中（3~4）   |   5.大（5~6）   |\n|_________________|_________________|_________________|\n|        |        |        |        |        |        |\n|   6.1  |   7.2  |   8.3  |   9.4  |  10.5  |  11.6  |\n|________|________|________|________|________|________|\n\n");
            	put("每注10元，买几张？"); int gs; scanf("%d",&gs); if(gs==0) { cin.ignore(); continue; }
            	string cost = to_string(10LL * gs); if(cmp(cost, ye) > 0){put("余额不足…\n");my_sleep(1000); cin.ignore(); continue;}
            	ye = sub(ye, cost); memset(cp6,0,sizeof(cp6)); long long sumcp=0;
            	put("你要押几号区域？（输入区域编号，空格隔开）\n"); for(int i=1,x;i<=gs;i++){ scanf("%d",&x); cp6[x]++; } cin.ignore(); clear_screen();
				for(int i=1;i<=25;i++){ outf("骰子滚动中... %d",rand()%6+1); my_sleep(50); gotoxy(0,0); }
				for(int i=1;i<=15;i++){ outf("骰子滚动中... %d",rand()%6+1); my_sleep(150); gotoxy(0,0); }
				for(int i=1;i<=4;i++){ outf("快停了... %d",rand()%6+1); my_sleep(400); gotoxy(0,0); }
				int ans=rand()%6+1; putf("开奖结果是： %d ！！\n",ans); my_sleep(250);
				sumcp += (cp6[ans/4+1]*20 + cp6[(ans-1)/2+3]*35 + cp6[ans+5]*70) * dev_rate_daxiao_mult; ye = add(ye, to_string(sumcp)); 
                putf("\n中了%lld元！\npress enter to continue…\n",sumcp); getchar();
			}
			else if(cpzl==7) {
				HideCursor(); clear_screen(); ans=0; putf("余额：%s\n买几个弹珠？（10元一个）-_-建议感受弹珠雨(100个以上)\n", ye.c_str());
				scanf("%d",&n); cin.ignore(); string cost = to_string(10LL * n); if(cmp(cost, ye) > 0) {put("余额不足...\n");my_sleep(1000);continue;}
				ye = sub(ye, cost); for(int i=1;i<=n;i++) pos[i]=25; clear_screen(); s[0][25]='o'; 
                for(int i=0;i<=13;i++) outf("%s\n",s[i]+1); s[0][25]=' ';
				for(int i=1;i<=12;i++){ 
					gotoxy(0,0); for(int j=1;j<=n;j++) s[i][pos[j]]='o';
					for(int j=0;j<=13;j++) outf("%s\n",s[j]+1); for(int j=1;j<=n;j++) s[i][pos[j]]=' ';
					if(i!=12){
						for(int j=1;j<=n;j++){
                            if(n<99){ if(rand()%2) pos[j]+=2; else pos[j]-=2; }
                            else{ if(pos[j]>=29){ if(rand()%3) pos[j]-=2; else pos[j]+=2; } else if(pos[j]<=21){ if(rand()%3) pos[j]+=2; else pos[j]-=2; } else { if(rand()%2) pos[j]+=2; else pos[j]-=2; } }
						}
					} my_sleep(i*50);
				}
				gotoxy(0,0); for(int i=0;i<=13;i++) outf("%s\n",s[i]+1);
				for(int i=1;i<=n;i++) ans+=shouyi[pos[i]]; ans = ans * dev_rate_danzhu_mult;
				putf("弹珠结算，共获得 %d 元！\n",ans); ye = add(ye, to_string(ans)); pause_screen();
			}
        }
        if(cz=="2") { 
            HideCursor(); clear_screen(); 
            put("|~~~~~~~~~~~~~~~~~|\n|     无穷彩票    |\n|中i元概率：1/3*i |\n|      零售价:10元|\n|                 |\n~~~~~~~~~~~~~~~~~~~\n\n");
            put("|~~~~~~~~~~~~~~~~~|\n|     传统彩票    |\n| 中10元概率：30% |\n| 中20元概率：10% |\n| 中50元概率：10% |\n|      零售价:10元|\n|                 |\n~~~~~~~~~~~~~~~~~~~\n\n");
            put("|~~~~~~~~~~~~~~~~~|\n|     幸运数字    |\n|   大奖：100元   |\n|    差3：50元    |\n|    差5：30元    |\n|   差10：20元    |\n|   差15：10元    |\n|      零售价:20元|\n|                 |\n~~~~~~~~~~~~~~~~~~~\n\n");
            put("|~~~~~~~~~~~~~~~~~|\n|      刮刮乐     |\n|      10:0~20    |\n|      20:0~40    |\n|      30:0~60    |\n|      50:0~100   |\n|      零售价:10元|\n|                 |\n~~~~~~~~~~~~~~~~~~~\n\n");
            put("|~~~~~~~~~~~~~~~~~|\n|      单色球     |\n|   每张6个数     |\n|   每中一个5元   |\n|     零售价：10元|\n|                 |\n~~~~~~~~~~~~~~~~~~~\n\n");
            put("press enter to continue…\n"); getchar(); 
        }
    }
}
```