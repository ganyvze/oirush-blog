---
title: 局域网聊天室管理控制台
published: 2026-01-08
description: "v1.1"
image: "./cover.jpeg"
tags: ["Dashboard"]
category: 后台
draft: false
---

## **[局域网聊天室客户端](https://ganyvze.pages.dev/posts/control)**

---

```shell
-std=c++11
```
```cpp
//局域网聊天室控制台 - v1.1
#include <iostream>
#include <string>
#include <vector>
#include <chrono>
#include <sstream>
#include <iomanip>
#include <regex>
#include <winsock2.h>
#include <windows.h>
#define BCRYPT_ECDSA_P256_ALGORITHM L"ECDSA_P256"
using namespace std;

// ----------公钥----------
const string ADMIN_PUB_KEY = "d27605efb07c8a1e79c41c85e48172b4ece6579ff810eb775606a294763aeb4c9ef8aa24b9c504005c8b959ceaa13b099a100fe4ecf45d15705821b83913f37e"; 
string admin_priv_key = ""; // 用户输入的私钥标量d
// ----------公钥----------


// ======= BCrypt (CNG) 动态加载相关的类型定义 =======
typedef PVOID BCRYPT_ALG_HANDLE;
typedef PVOID BCRYPT_KEY_HANDLE;
typedef PVOID BCRYPT_HASH_HANDLE;
#ifndef NTSTATUS
typedef LONG NTSTATUS;
#endif

typedef int(WSAAPI *P_WS)(WORD, LPWSADATA); typedef int(WSAAPI *P_WC)(void); typedef SOCKET(WSAAPI *P_SK)(int, int, int);
typedef int(WSAAPI *P_CS)(SOCKET); typedef int(WSAAPI *P_SD)(SOCKET, const char*, int, int);
typedef int(WSAAPI *P_ST)(SOCKET, const char*, int, int, const struct sockaddr*, int);
typedef int(WSAAPI *P_RF)(SOCKET, char*, int, int, struct sockaddr*, int*);
typedef int(WSAAPI *P_SO)(SOCKET, int, int, const char*, int); typedef u_short(WSAAPI *P_HS)(u_short);
typedef unsigned long(WSAAPI *P_IA)(const char*); typedef char*(WSAAPI *P_IN)(struct in_addr);

typedef NTSTATUS(WINAPI *P_BCryptOpenAlgorithmProvider)(BCRYPT_ALG_HANDLE*, LPCWSTR, LPCWSTR, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptCloseAlgorithmProvider)(BCRYPT_ALG_HANDLE, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptImportKeyPair)(BCRYPT_ALG_HANDLE, BCRYPT_KEY_HANDLE, LPCWSTR, BCRYPT_KEY_HANDLE*, PUCHAR, ULONG, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptCreateHash)(BCRYPT_ALG_HANDLE, BCRYPT_HASH_HANDLE*, PUCHAR, ULONG, PUCHAR, ULONG, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptHashData)(BCRYPT_HASH_HANDLE, PUCHAR, ULONG, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptFinishHash)(BCRYPT_HASH_HANDLE, PUCHAR, ULONG, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptDestroyHash)(BCRYPT_HASH_HANDLE);
typedef NTSTATUS(WINAPI *P_BCryptSignHash)(BCRYPT_KEY_HANDLE, VOID*, PUCHAR, ULONG, PUCHAR, ULONG, ULONG*, ULONG);
typedef NTSTATUS(WINAPI *P_BCryptDestroyKey)(BCRYPT_KEY_HANDLE);

struct API_HUB {
    HMODULE hS; P_WS ws; P_WC wc; P_SK sk; P_CS cs; P_ST st; P_RF rf; P_SO so; P_HS hs; P_IA ia; P_IN in;
    HMODULE hBcrypt;
    P_BCryptOpenAlgorithmProvider bOpenAlg; P_BCryptCloseAlgorithmProvider bCloseAlg;
    P_BCryptImportKeyPair bImportKey; P_BCryptCreateHash bCreateHash; P_BCryptHashData bHashData;
    P_BCryptFinishHash bFinishHash; P_BCryptDestroyHash bDestroyHash;
    P_BCryptSignHash bSignHash; P_BCryptDestroyKey bDestroyKey;

    void init() {
        hS = LoadLibraryA("ws2_32.dll");
        ws = (P_WS)GetProcAddress(hS, "WSAStartup"); wc = (P_WC)GetProcAddress(hS, "WSACleanup");
        sk = (P_SK)GetProcAddress(hS, "socket"); cs = (P_CS)GetProcAddress(hS, "closesocket");
        st = (P_ST)GetProcAddress(hS, "sendto"); rf = (P_RF)GetProcAddress(hS, "recvfrom");
        so = (P_SO)GetProcAddress(hS, "setsockopt"); hs = (P_HS)GetProcAddress(hS, "htons");
        ia = (P_IA)GetProcAddress(hS, "inet_addr"); in = (P_IN)GetProcAddress(hS, "inet_ntoa");

        hBcrypt = LoadLibraryA("bcrypt.dll");
        if (hBcrypt) {
            bOpenAlg = (P_BCryptOpenAlgorithmProvider)GetProcAddress(hBcrypt, "BCryptOpenAlgorithmProvider");
            bCloseAlg = (P_BCryptCloseAlgorithmProvider)GetProcAddress(hBcrypt, "BCryptCloseAlgorithmProvider");
            bImportKey = (P_BCryptImportKeyPair)GetProcAddress(hBcrypt, "BCryptImportKeyPair");
            bCreateHash = (P_BCryptCreateHash)GetProcAddress(hBcrypt, "BCryptCreateHash");
            bHashData = (P_BCryptHashData)GetProcAddress(hBcrypt, "BCryptHashData");
            bFinishHash = (P_BCryptFinishHash)GetProcAddress(hBcrypt, "BCryptFinishHash");
            bDestroyHash = (P_BCryptDestroyHash)GetProcAddress(hBcrypt, "BCryptDestroyHash");
            bSignHash = (P_BCryptSignHash)GetProcAddress(hBcrypt, "BCryptSignHash");
            bDestroyKey = (P_BCryptDestroyKey)GetProcAddress(hBcrypt, "BCryptDestroyKey");
        }
    }
} API;

SOCKET udp_socket = INVALID_SOCKET;
sockaddr_in broadcast_addr;

long long wall_now_ms() {
    return std::chrono::duration_cast<std::chrono::milliseconds>(
        std::chrono::system_clock::now().time_since_epoch()
    ).count();
}

vector<BYTE> hex2bytes(const string& hex) {
    vector<BYTE> bytes;
    for (size_t i = 0; i < hex.length(); i += 2) {
        bytes.push_back((BYTE)strtol(hex.substr(i, 2).c_str(), NULL, 16));
    }
    return bytes;
}

string bytes2hex(const vector<BYTE>& bytes) {
    stringstream ss;
    ss << hex << setfill('0');
    for (auto b : bytes) ss << setw(2) << (int)b;
    return ss.str();
}

bool is_valid_ip(const string& ip) {
    regex ip_regex("^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$");
    return regex_match(ip, ip_regex);
}

string trim(const string& s) {
    size_t start = s.find_first_not_of(" \t\r\n");
    if (start == string::npos) return "";
    return s.substr(start, s.find_last_not_of(" \t\r\n") - start + 1);
}

// 基于 ECDSA P-256 对消息进行签名
string sign_message(const string& msg) {
    if (admin_priv_key.length() != 64) return "";

    vector<BYTE> pub_bytes = hex2bytes(ADMIN_PUB_KEY);
    vector<BYTE> priv_bytes = hex2bytes(admin_priv_key);
    
    if (pub_bytes.size() != 64 || priv_bytes.size() != 32) return "";

    #pragma pack(push, 1)
    struct {
        ULONG dwMagic; // BCRYPT_ECDSA_PRIVATE_P256_MAGIC (0x32534345)
        ULONG cbKey;
        BYTE X[32];
        BYTE Y[32];
        BYTE d[32];
    } eccBlob;
    #pragma pack(pop)

    eccBlob.dwMagic = 0x32534345; // "ESC2"
    eccBlob.cbKey = 32;
    memcpy(eccBlob.X, pub_bytes.data(), 32);
    memcpy(eccBlob.Y, pub_bytes.data() + 32, 32);
    memcpy(eccBlob.d, priv_bytes.data(), 32);

    BCRYPT_ALG_HANDLE hSignAlg = NULL, hHashAlg = NULL;
    BCRYPT_KEY_HANDLE hKey = NULL;
    BCRYPT_HASH_HANDLE hHash = NULL;
    string signature_hex = "";
    NTSTATUS status;

    do {
        // 使用更具体的算法名称
        status = API.bOpenAlg(&hSignAlg, BCRYPT_ECDSA_P256_ALGORITHM, NULL, 0);
        if (status != 0) break;

        status = API.bImportKey(hSignAlg, NULL, L"ECCPRIVATEBLOB", &hKey, (PUCHAR)&eccBlob, sizeof(eccBlob), 0);
        if (status != 0) {
            cout << "\n[错误] 私钥导入失败（这里报错通常是因为公钥和输入的私钥不匹配）, Status: 0x" << hex << status << dec << endl;
            break;
        }
        
        status = API.bOpenAlg(&hHashAlg, L"SHA256", NULL, 0);
        if (status != 0) break;

        status = API.bCreateHash(hHashAlg, &hHash, NULL, 0, NULL, 0, 0);
        if (status != 0) break;

        status = API.bHashData(hHash, (PUCHAR)msg.data(), (ULONG)msg.size(), 0);
        if (status != 0) break;
        
        BYTE hashResult[32];
        status = API.bFinishHash(hHash, hashResult, sizeof(hashResult), 0);
        if (status != 0) break;

        ULONG cbSig = 0;
        status = API.bSignHash(hKey, NULL, hashResult, sizeof(hashResult), NULL, 0, &cbSig, 0);
        if (status != 0) break;

        vector<BYTE> sig(cbSig);
        status = API.bSignHash(hKey, NULL, hashResult, sizeof(hashResult), sig.data(), cbSig, &cbSig, 0);
        if (status == 0) {
            signature_hex = bytes2hex(sig);
        }
    } while (false);

    if (hHash) API.bDestroyHash(hHash);
    if (hKey) API.bDestroyKey(hKey);
    if (hHashAlg) API.bCloseAlg(hHashAlg, 0);
    if (hSignAlg) API.bCloseAlg(hSignAlg, 0);

    return signature_hex;
}

void send_admin_command(const string& body) {
    long long ts = wall_now_ms();
    string data_to_verify = to_string(ts) + "\n" + body;
    string sig = sign_message(data_to_verify);

    if (sig.empty()) {
        cout << "[错误] 签名失败，私钥可能无效或系统不支持。" << endl;
        return;
    }

    string packet = "LANCHAT1\nTYPE=ADMIN_CMD\nTS=" + to_string(ts) + "\nSIG=" + sig + "\n\n" + body;
    API.st(udp_socket, packet.c_str(), (int)packet.size(), 0, (struct sockaddr*)&broadcast_addr, sizeof(broadcast_addr));
}

void handle_list_command() {
    send_admin_command("list");
    
    cout << "=> 已发送探测广播，正在监听响应 (2秒)..." << endl;
    
    // 设置非阻塞接收超时 2秒
    int timeout = 2000;
    API.so(udp_socket, SOL_SOCKET, SO_RCVTIMEO, (char*)&timeout, sizeof(timeout));
    
    long long start = wall_now_ms();
    int count = 0;

    while (wall_now_ms() - start < 2000) {
        char buf[2048];
        sockaddr_in from;
        int flen = sizeof(from);
        int r_len = API.rf(udp_socket, buf, sizeof(buf), 0, (sockaddr*)&from, &flen);
        
        if (r_len > 0) {
            string raw(buf, r_len);
            if (raw.find("TYPE=ADMIN_REP") != string::npos && raw.find("ONLINE") != string::npos) {
                cout << "  [+] 发现存活节点 IP: " << API.in(from.sin_addr) << endl;
                count++;
            }
        }
    }
    cout << "=> 扫描完毕，共响应 " << count << " 个存活节点。" << endl;
}

void print_help() {
    cout << "================== 支持的命令 ==================\n";
    cout << "  list                 - 扫描局域网内所有在线用户\n";
    cout << "  remove <ip>          - 强制踢出指定IP的用户\n";
    cout << "  blacklist <ip>       - 将指定IP加入黑名单并踢出\n";
    cout << "  whitelist <ip>       - 将指定IP移出黑名单\n";
    cout << "  help                 - 查看此帮助信息\n";
    cout << "  exit                 - 退出程序\n";
    cout << "================================================\n";
}

int main() {
    system("chcp 936 > nul");
    API.init();
    WSADATA w;
    API.ws(MAKEWORD(2, 2), &w);

    udp_socket = API.sk(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
    int opt = 1;
    API.so(udp_socket, SOL_SOCKET, SO_BROADCAST, (char*)&opt, sizeof(opt));

    memset(&broadcast_addr, 0, sizeof(broadcast_addr));
    broadcast_addr.sin_family = AF_INET;
    broadcast_addr.sin_port = API.hs(8888);
    broadcast_addr.sin_addr.s_addr = API.ia("255.255.255.255");

    cout << "=================================================\n";
    cout << "          局域网聊天室 - 管理控制台 (v1.0)       \n";
    cout << "=================================================\n\n";

    // 验证私钥
    while (1) {
        cout << "请输入验证令牌: ";
        string input_key;
        getline(cin, input_key);
        input_key = trim(input_key);
        
        if (input_key.length() == 64 && input_key.find_first_not_of("0123456789abcdefABCDEF") == string::npos) {
            admin_priv_key = input_key;
            cout << "[成功] 密钥格式有效！即将进入控制台...\n";
            system("pause");
            break;
        } else {
            cout << "[错误] 无效的私钥格式！";
            system("cls");
        }
    }
    system("cls");
    // 交互式命令主循环
    while (1) {
        cout << "> ";
        string cmd;
        getline(cin, cmd);
        cmd = trim(cmd);
        if (cmd.empty()) continue;

        if (cmd == "exit") {
            break;
        } else if (cmd == "help") {
            print_help();
        } else if (cmd == "list") {
            handle_list_command();
        } else if (cmd.find("remove ") == 0) {
            string ip = trim(cmd.substr(7));
            if (is_valid_ip(ip)) {
                send_admin_command("remove " + ip);
                cout << "=> 已下发踢出指令给 IP: " << ip << endl;
            } else {
                cout << "[错误] 无效的IP地址格式！" << endl;
            }
        } else if (cmd.find("blacklist ") == 0) {
            string ip = trim(cmd.substr(10));
            if (is_valid_ip(ip)) {
                send_admin_command("blacklist " + ip);
                cout << "=> 已下发拉黑指令给 IP: " << ip << endl;
            } else {
                cout << "[错误] 无效的IP地址格式！" << endl;
            }
        } else if (cmd.find("whitelist ") == 0) {
            string ip = trim(cmd.substr(10));
            if (is_valid_ip(ip)) {
                send_admin_command("whitelist " + ip);
                cout << "=> 已下发解除黑名单指令给 IP: " << ip << endl;
            } else {
                cout << "[错误] 无效的IP地址格式！" << endl;
            }
        } else {
            cout << "[错误] 未知命令！输入 'help' 查看支持的指令。" << endl;
        }
    }API.cs(udp_socket);API.wc();
    return 0;
}
```