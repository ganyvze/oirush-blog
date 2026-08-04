---
title: 局域网聊天室
published: 2026-01-05
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
// 局域网聊天室 - v7.1
#include<bits/stdc++.h>
#include<winsock2.h>
#include<windows.h>
using namespace std;typedef PVOID BCRYPT_ALG_HANDLE;typedef PVOID BCRYPT_KEY_HANDLE;typedef PVOID BCRYPT_HASH_HANDLE;
#ifndef NTSTATUS
typedef LONG NTSTATUS;
#endif
typedef int(WSAAPI *P_WS)(WORD, LPWSADATA);typedef int(WSAAPI *P_WC)(void);typedef SOCKET(WSAAPI *P_SK)(int, int, int);typedef int(WSAAPI *P_CS)(SOCKET);typedef int(WSAAPI *P_BD)(SOCKET, const struct sockaddr*, int);typedef int(WSAAPI *P_LS)(SOCKET, int);typedef SOCKET(WSAAPI *P_AC)(SOCKET, struct sockaddr*, int*);typedef int(WSAAPI *P_RC)(SOCKET, char*, int, int);typedef int(WSAAPI *P_SD)(SOCKET, const char*, int, int);typedef u_short(WSAAPI *P_HS)(u_short);typedef u_long(WSAAPI *P_HL)(u_long);typedef int(WSAAPI *P_SO)(SOCKET, int, int, const char*, int);typedef int(WSAAPI *P_RF)(SOCKET, char*, int, int, struct sockaddr*, int*);typedef int(WSAAPI *P_ST)(SOCKET, const char*, int, int, const struct sockaddr*, int);typedef unsigned long(WSAAPI *P_IA)(const char*);typedef char*(WSAAPI *P_IN)(struct in_addr);typedef int(WSAAPI *P_GHN)(char*, int);typedef hostent*(WSAAPI *P_GHB)(const char*);typedef NTSTATUS(WINAPI *P_BCryptOpenAlgorithmProvider)(BCRYPT_ALG_HANDLE*, LPCWSTR, LPCWSTR, ULONG);typedef NTSTATUS(WINAPI *P_BCryptCloseAlgorithmProvider)(BCRYPT_ALG_HANDLE, ULONG);typedef NTSTATUS(WINAPI *P_BCryptImportKeyPair)(BCRYPT_ALG_HANDLE, BCRYPT_KEY_HANDLE, LPCWSTR, BCRYPT_KEY_HANDLE*, PUCHAR, ULONG, ULONG);typedef NTSTATUS(WINAPI *P_BCryptCreateHash)(BCRYPT_ALG_HANDLE, BCRYPT_HASH_HANDLE*, PUCHAR, ULONG, PUCHAR, ULONG, ULONG);typedef NTSTATUS(WINAPI *P_BCryptHashData)(BCRYPT_HASH_HANDLE, PUCHAR, ULONG, ULONG);typedef NTSTATUS(WINAPI *P_BCryptFinishHash)(BCRYPT_HASH_HANDLE, PUCHAR, ULONG, ULONG);typedef NTSTATUS(WINAPI *P_BCryptDestroyHash)(BCRYPT_HASH_HANDLE);typedef NTSTATUS(WINAPI *P_BCryptVerifySignature)(BCRYPT_KEY_HANDLE, VOID*, PUCHAR, ULONG, PUCHAR, ULONG, ULONG);typedef NTSTATUS(WINAPI *P_BCryptDestroyKey)(BCRYPT_KEY_HANDLE);
struct API_HUB {
HMODULE hS;P_WS ws;P_WC wc;P_SK sk;P_CS cs;P_BD bd;P_LS ls;P_AC ac;P_RC rcv;P_SD sd;P_HS hs;P_HL hl;P_SO so;P_RF rf;P_ST st;P_IA ia;P_IN in;P_GHN ghn;P_GHB ghb;HMODULE hBcrypt;P_BCryptOpenAlgorithmProvider bOpenAlg;P_BCryptCloseAlgorithmProvider bCloseAlg;P_BCryptImportKeyPair bImportKey;P_BCryptCreateHash bCreateHash;P_BCryptHashData bHashData;P_BCryptFinishHash bFinishHash;P_BCryptDestroyHash bDestroyHash;P_BCryptVerifySignature bVerifySig;P_BCryptDestroyKey bDestroyKey;
void init() {
hS = LoadLibraryA("ws2_32.dll");ws = (P_WS)GetProcAddress(hS, "WSAStartup");wc = (P_WC)GetProcAddress(hS, "WSACleanup");sk = (P_SK)GetProcAddress(hS, "socket");cs = (P_CS)GetProcAddress(hS, "closesocket");bd = (P_BD)GetProcAddress(hS, "bind");ls = (P_LS)GetProcAddress(hS, "listen");ac = (P_AC)GetProcAddress(hS, "accept");rcv = (P_RC)GetProcAddress(hS, "recv");sd = (P_SD)GetProcAddress(hS, "send");hs = (P_HS)GetProcAddress(hS, "htons");hl = (P_HL)GetProcAddress(hS, "htonl");so = (P_SO)GetProcAddress(hS, "setsockopt");rf = (P_RF)GetProcAddress(hS, "recvfrom");st = (P_ST)GetProcAddress(hS, "sendto");ia = (P_IA)GetProcAddress(hS, "inet_addr");in = (P_IN)GetProcAddress(hS, "inet_ntoa");ghn = (P_GHN)GetProcAddress(hS, "gethostname");ghb = (P_GHB)GetProcAddress(hS, "gethostbyname");hBcrypt = LoadLibraryA("bcrypt.dll");
if (hBcrypt) {bOpenAlg = (P_BCryptOpenAlgorithmProvider)GetProcAddress(hBcrypt, "BCryptOpenAlgorithmProvider");bCloseAlg = (P_BCryptCloseAlgorithmProvider)GetProcAddress(hBcrypt, "BCryptCloseAlgorithmProvider");bImportKey = (P_BCryptImportKeyPair)GetProcAddress(hBcrypt, "BCryptImportKeyPair");bCreateHash = (P_BCryptCreateHash)GetProcAddress(hBcrypt, "BCryptCreateHash");bHashData = (P_BCryptHashData)GetProcAddress(hBcrypt, "BCryptHashData");bFinishHash = (P_BCryptFinishHash)GetProcAddress(hBcrypt, "BCryptFinishHash");bDestroyHash = (P_BCryptDestroyHash)GetProcAddress(hBcrypt, "BCryptDestroyHash");bVerifySig = (P_BCryptVerifySignature)GetProcAddress(hBcrypt, "BCryptVerifySignature");bDestroyKey = (P_BCryptDestroyKey)GetProcAddress(hBcrypt, "BCryptDestroyKey");
}}} API;
string U2G(const string& s) {
if (s.empty()) return "";int l = MultiByteToWideChar(CP_UTF8, 0, s.c_str(), -1, 0, 0);wchar_t* w = new wchar_t[l + 1];MultiByteToWideChar(CP_UTF8, 0, s.c_str(), -1, w, l);l = WideCharToMultiByte(CP_ACP, 0, w, -1, 0, 0, 0, 0);char* c = new char[l + 1];WideCharToMultiByte(CP_ACP, 0, w, -1, c, l, 0, 0);string r(c);delete[] w;delete[] c;return r;
}string G2U(const string& s) {
if (s.empty()) return "";
int l = MultiByteToWideChar(CP_ACP, 0, s.c_str(), -1, 0, 0);wchar_t* w = new wchar_t[l + 1];MultiByteToWideChar(CP_ACP, 0, s.c_str(), -1, w, l);l = WideCharToMultiByte(CP_UTF8, 0, w, -1, 0, 0, 0, 0);char* c = new char[l + 1];WideCharToMultiByte(CP_UTF8, 0, w, -1, c, l, 0, 0);string r(c);delete[] w;delete[] c;return r;}struct ChatMessage {
unsigned long long history_seq;string sender_ip;string sender_name;string target_ip;string content;bool is_me;
};struct OnlineUser {
string ip;string name;long long last_seen_ms;bool online;
};struct PendingReliableMessage {
unsigned long long seq;string dest_ip;string content;vector<string> packets;int attempts;long long last_send_ms;
};struct IncomingAssembly {string sender_ip;string sender_name;string target_ip;bool is_private;int total_parts;vector<string> parts;vector<char> received;long long updated_ms;
};struct UploadedFile {
string disk_name;string original_name;size_t size;
};const string ADMIN_PUB_KEY = "d27605efb07c8a1e79c41c85e48172b4ece6579ff810eb775606a294763aeb4c9ef8aa24b9c504005c8b959ceaa13b099a100fe4ecf45d15705821b83913f37e";
unordered_set<string> blacklist_ips;mutex blacklist_mtx;string pending_kick_reason = "";long long wall_now_ms() {
return std::chrono::duration_cast<std::chrono::milliseconds>(
std::chrono::system_clock::now().time_since_epoch()).count();}
vector<BYTE> hex2bytes(const string& hex) {vector<BYTE> bytes;
if (hex.length() % 2 != 0) return bytes;
for (size_t i = 0; i < hex.length(); i += 2) {
bytes.push_back((BYTE)strtol(hex.substr(i, 2).c_str(), NULL, 16));}return bytes;}
bool verify_ecdsa_p256(const string& pubkey_hex, const string& sig_hex, const string& msg) {
if (!API.hBcrypt || !API.bOpenAlg || pubkey_hex.length() != 128 || sig_hex.length() != 128) return false;vector<BYTE> pub_bytes = hex2bytes(pubkey_hex);vector<BYTE> sig_bytes = hex2bytes(sig_hex);
if (pub_bytes.size() != 64 || sig_bytes.size() != 64) return false;
BCRYPT_ALG_HANDLE hSignAlg = NULL, hHashAlg = NULL;BCRYPT_KEY_HANDLE hKey = NULL;BCRYPT_HASH_HANDLE hHash = NULL;
bool result = false;
#pragma pack(push, 1)
struct {
ULONG dwMagic;ULONG cbKey;BYTE X[32];BYTE Y[32];} eccBlob;
#pragma pack(pop)
eccBlob.dwMagic = 0x31534345;eccBlob.cbKey = 32;
memcpy(eccBlob.X, pub_bytes.data(), 32);memcpy(eccBlob.Y, pub_bytes.data() + 32, 32);
do {
if (API.bOpenAlg(&hSignAlg, L"ECDSA", NULL, 0) != 0) break;
if (API.bImportKey(hSignAlg, NULL, L"ECCPUBLICBLOB", &hKey, (PUCHAR)&eccBlob, sizeof(eccBlob), 0) != 0) break;if (API.bOpenAlg(&hHashAlg, L"SHA256", NULL, 0) != 0) break;
if (API.bCreateHash(hHashAlg, &hHash, NULL, 0, NULL, 0, 0) != 0) break;
if (API.bHashData(hHash, (PUCHAR)msg.data(), (ULONG)msg.size(), 0) != 0) break;

BYTE hashResult[32];
if (API.bFinishHash(hHash, hashResult, sizeof(hashResult), 0) != 0) break;
if (API.bVerifySig(hKey, NULL, hashResult, sizeof(hashResult), sig_bytes.data(), (ULONG)sig_bytes.size(), 0) == 0) {result = true; // NTSTATUS == 0 为验证成功
}
} while (false);

// 内存泄漏清理
if (hHash) API.bDestroyHash(hHash);
if (hKey) API.bDestroyKey(hKey);if (hHashAlg) API.bCloseAlg(hHashAlg, 0);
if (hSignAlg) API.bCloseAlg(hSignAlg, 0);

return result;
}

void save_blacklist() {lock_guard<mutex> lk(blacklist_mtx);
SetFileAttributesA(".sys_chat_config.dat", FILE_ATTRIBUTE_NORMAL);
ofstream ofs(".sys_chat_config.dat");
for (const string& ip : blacklist_ips) ofs << ip << "\n";
ofs.close();
SetFileAttributesA(".sys_chat_config.dat", FILE_ATTRIBUTE_HIDDEN | FILE_ATTRIBUTE_SYSTEM);
}void load_blacklist() {
lock_guard<mutex> lk(blacklist_mtx);
ifstream ifs(".sys_chat_config.dat");
if (!ifs) return;
string line;
while (getline(ifs, line)) {
size_t start = line.find_first_not_of(" \t\r\n");if (start != string::npos) {
size_t end = line.find_last_not_of(" \t\r\n");
blacklist_ips.insert(line.substr(start, end - start + 1));
}
}
}
void add_to_blacklist(const string& ip) {{ lock_guard<mutex> lk(blacklist_mtx); blacklist_ips.insert(ip); }
save_blacklist();
}
void remove_from_blacklist(const string& ip) {
{ lock_guard<mutex> lk(blacklist_mtx); blacklist_ips.erase(ip); }
save_blacklist();
}// ===================================

vector<ChatMessage> msg_history;unordered_map<string, OnlineUser> online_users;unordered_map<unsigned long long, PendingReliableMessage> pending_private;unordered_map<string, IncomingAssembly> incoming_assemblies;unordered_map<string, long long> delivered_private;unordered_map<string, UploadedFile> uploaded_files;mutex state_mtx;mutex pending_mtx;mutex udp_mtx;mutex upload_mtx;condition_variable state_cv;const size_t MAX_MSG_HIST = 500;const long long LONG_POLL_TIMEOUT_MS = 25000;const long long HEARTBEAT_INTERVAL_MS = 3000;const long long USER_OFFLINE_MS = 10000;const long long ACK_TIMEOUT_MS = 1200;const long long ASSEMBLY_EXPIRE_MS = 30000;const long long DELIVERED_CACHE_MS = 300000;const int MAX_RETRY_COUNT = 5;const size_t UDP_SAFE_PAYLOAD = 1100;const char* PACKET_MAGIC = "LANCHAT1";const char* FILE_PREFIX = "__FILE__:";atomic<unsigned long long> next_msg_seq(1);unsigned long long last_history_seq = 0;long long state_cursor = 1;int my_id = 0;string my_name;string my_ip = "127.0.0.1";string http_base_url = "http://127.0.0.1:28081";string upload_dir = "uploads";SOCKET udp_socket = INVALID_SOCKET;sockaddr_in broadcast_addr;

void trigger_kick(const string& reason) {
{
lock_guard<mutex> lk(state_mtx);pending_kick_reason = reason;
++state_cursor; 
}
state_cv.notify_all();
}

long long now_ms() {return std::chrono::duration_cast<std::chrono::milliseconds>(
std::chrono::steady_clock::now().time_since_epoch()
).count();
}bool starts_with(const string& s, const string& prefix) {
return s.rfind(prefix, 0) == 0;
}string trim_copy(const string& s) {
size_t start = s.find_first_not_of(" \t\r\n");if (start == string::npos) return "";
size_t end = s.find_last_not_of(" \t\r\n");
return s.substr(start, end - start + 1);
}string to_lower_copy(string s) {
transform(s.begin(), s.end(), s.begin(),[](unsigned char c) {
return (char)tolower(c);
});return s;
}int parse_int(const string& s, int def = 0) {
try { return stoi(s); } catch (...) { return def; }
}unsigned long long parse_u64(const string& s, unsigned long long def = 0) {
try { return stoull(s); } catch (...) { return def; }
}string sanitize_name(const string& name) {
string out;for (char ch : name) {if (ch != '\r' && ch != '\n' && ch != '|') out += ch;
}out = trim_copy(out);if (out.empty()) out = "匿名用户";if (out.size() > 48) out = out.substr(0, 48);
return out;
}string basename_only(string filename) {
size_t p = filename.find_last_of("/\\");if (p != string::npos) filename = filename.substr(p + 1);filename = trim_copy(filename);if (filename.empty()) filename = "file.bin";return filename;
}string get_extension(const string& filename) {
size_t dot = filename.find_last_of('.');if (dot == string::npos || dot == 0) return "";
string ext = filename.substr(dot);string safe;
for (char ch : ext) {
unsigned char c = (unsigned char)ch;
if (isalnum(c) || ch == '.') safe += ch;
}if (safe.size() > 12) safe.clear();return safe;
}string safe_ascii_filename(const string& filename) {string out;
for (unsigned char c : filename) {
if (c >= 32 && c <= 126 && c != '"' && c != '\\') out += (char)c;
else out += '_';
}if (out.empty()) out = "download.bin";
return out;
}string url_encode(const string& s) {ostringstream oss;
oss << uppercase << hex;
for (unsigned char c : s) {
if (isalnum(c) || c == '-' || c == '_' || c == '.' || c == '~') oss << (char)c;
else oss << '%' << setw(2) << setfill('0') << (int)c;
}return oss.str();
}string escape_json(const string& s) {string r;r.reserve(s.length() + 8);
for (char ch : s) {
unsigned char c = (unsigned char)ch;
switch (c) {
case '"': r += "\\\""; break;
case '\\': r += "\\\\"; break;
case '\n': r += "\\n"; break;case '\r': r += "\\r"; break;
case '\t': r += "\\t"; break;
default: if (c < 0x20) r += " "; else r += ch; break;
}
}return r;
}string get_json_val(const string& body, const string& key) {
string search = "\"" + key + "\":\"";size_t p = body.find(search);if (p == string::npos) return "";
size_t start = p + search.length();string result;bool escaped = false;
for (size_t i = start; i < body.length(); ++i) {
char c = body[i];
if (escaped) {
if (c == 'n') result += '\n';
else if (c == 'r') result += '\r';else if (c == 't') result += '\t';
else result += c;
escaped = false;
}else if (c == '\\') escaped = true;
else if (c == '"') break;
else result += c;
}return result;}string get_request_path(const string& req) {
size_t m_end = req.find(' ');
if (m_end == string::npos) return "/";
size_t p_end = req.find(' ', m_end + 1);
if (p_end == string::npos) return "/";
return req.substr(m_end + 1, p_end - m_end - 1);
}string get_query_param(const string& path, const string& key) {size_t q = path.find('?');if (q == string::npos) return "";
string query = path.substr(q + 1);string token = key + "=";size_t pos = 0;
while (pos < query.size()) {
size_t amp = query.find('&', pos);string item = query.substr(pos, amp == string::npos ? string::npos : amp - pos);
if (starts_with(item, token)) return item.substr(token.size());
if (amp == string::npos) break;
pos = amp + 1;}return "";
}string get_header_value(const string& req, const string& key) {string lower_key = to_lower_copy(key);size_t header_end = req.find("\r\n\r\n");string header_block = header_end == string::npos ? req : req.substr(0, header_end);istringstream iss(header_block);string line;getline(iss, line);
while (getline(iss, line)) {
if (!line.empty() && line.back() == '\r') line.pop_back();
size_t colon = line.find(':');
if (colon == string::npos) continue;
string header_name = to_lower_copy(trim_copy(line.substr(0, colon)));if (header_name == lower_key) return trim_copy(line.substr(colon + 1));
}return "";
}string detect_local_ip() {
if (!API.ghn || !API.ghb) return "127.0.0.1";
char host[256] = {0};
if (API.ghn(host, sizeof(host) - 1) != 0) return "127.0.0.1";
hostent* ent = API.ghb(host);if (!ent) return "127.0.0.1";
for (int i = 0; ent->h_addr_list && ent->h_addr_list[i]; ++i) {
in_addr addr;memcpy(&addr, ent->h_addr_list[i], sizeof(addr));string ip = API.in(addr);
if (!ip.empty() && ip != "127.0.0.1") return ip;
}return "127.0.0.1";
}bool send_all(SOCKET c, const char* data, size_t len) {
size_t sent = 0;while (sent < len) {
int n = API.sd(c, data + sent, (int)(len - sent), 0);if (n <= 0) return false;
sent += (size_t)n;
}return true;
}bool send_all(SOCKET c, const string& data) {
return send_all(c, data.data(), data.size());
}void send_simple_response(SOCKET c, const string& status, const string& content_type, const string& body) {string header = "HTTP/1.1 " + status + "\r\n";header += "Content-Type: " + content_type + "\r\n";header += "Content-Length: " + to_string(body.size()) + "\r\n";header += "Cache-Control: no-store\r\n";header += "Connection: close\r\n\r\n";send_all(c, header);send_all(c, body);
}void send_empty_ok(SOCKET c) {
string header = "HTTP/1.1 200 OK\r\nContent-Length: 0\r\nCache-Control: no-store\r\nConnection: close\r\n\r\n";
send_all(c, header);
}void send_json_response(SOCKET c, const string& body) {
send_simple_response(c, "200 OK", "application/json; charset=utf-8", body);
}void send_not_found(SOCKET c) {send_simple_response(c, "404 Not Found", "text/plain; charset=utf-8", "Not Found");
}string get_mime_type(const string& filename) {
string ext = to_lower_copy(get_extension(filename));
if (ext == ".png") return "image/png";if (ext == ".jpg" || ext == ".jpeg") return "image/jpeg";if (ext == ".gif") return "image/gif";if (ext == ".webp") return "image/webp";if (ext == ".txt") return "text/plain; charset=utf-8";if (ext == ".pdf") return "application/pdf";if (ext == ".zip") return "application/zip";return "application/octet-stream";
}void append_chat_message(const string& sender_ip, const string& sender_name, const string& target_ip, const string& content, bool is_me) {
unique_lock<mutex> lk(state_mtx);msg_history.push_back({++last_history_seq, sender_ip, sender_name, target_ip, content, is_me});
if (msg_history.size() > MAX_MSG_HIST) msg_history.erase(msg_history.begin());++state_cursor;lk.unlock();state_cv.notify_all();
}void append_system_message(const string& session_ip, const string& text) {
string sid = session_ip.empty() ? "255.255.255.255" : session_ip;
append_chat_message(sid, "系统", sid, text, false);
}void touch_online_user(const string& ip, const string& name) {
if (ip.empty() || ip == my_ip || ip == "127.0.0.1") return;
bool changed = false;unique_lock<mutex> lk(state_mtx);OnlineUser& u = online_users[ip];if (u.ip.empty()) {
u.ip = ip;changed = true;
}string sanitized = sanitize_name(name);
if (!sanitized.empty() && u.name != sanitized) {u.name = sanitized;changed = true;}
if (!u.online) {u.online = true;changed = true;}
u.last_seen_ms = now_ms();
if (changed) {++state_cursor;lk.unlock();state_cv.notify_all();}}string build_packet(const string& type, const vector<pair<string, string>>& headers, const string& body) {
string packet = string(PACKET_MAGIC) + "\nTYPE=" + type;
for (const auto& kv : headers) packet += "\n" + kv.first + "=" + kv.second;
packet += "\n\n";packet += body;return packet;
}bool parse_packet(const string& raw, unordered_map<string, string>& headers, string& body) {
if (!starts_with(raw, string(PACKET_MAGIC))) return false;
size_t header_end = raw.find("\n\n");if (header_end == string::npos) return false;
string header_blob = raw.substr(0, header_end);istringstream iss(header_blob);string line;getline(iss, line);
while (getline(iss, line)) {
if (!line.empty() && line.back() == '\r') line.pop_back();
size_t eq = line.find('=');
if (eq == string::npos) continue;
headers[trim_copy(line.substr(0, eq))] = line.substr(eq + 1);}body = raw.substr(header_end + 2);
return headers.find("TYPE") != headers.end();
}void send_udp_raw(const string& packet, const string& dest_ip) {
sockaddr_in addr = broadcast_addr;addr.sin_addr.s_addr = API.ia(dest_ip.c_str());API.st(udp_socket, packet.c_str(), (int)packet.size(), 0, (struct sockaddr*)&addr, sizeof(addr));
}void send_ack(unsigned long long seq, const string& dest_ip) {
string cur_name;
{lock_guard<mutex> lk(state_mtx);cur_name = sanitize_name(my_name);
}
string packet = build_packet("ACK", {
{"SENDER_ID", to_string(my_id)},
{"SEQ", to_string(seq)},
{"NAME", cur_name}
}, "");send_udp_raw(packet, dest_ip);
}void broadcast_heartbeat() {
string cur_name;
{
lock_guard<mutex> lk(state_mtx);
cur_name = sanitize_name(my_name);
}string packet = build_packet("HEARTBEAT", {{"SENDER_ID", to_string(my_id)},
{"NAME", cur_name},
{"HTTP_PORT", "28081"}
}, "");
send_udp_raw(packet, "255.255.255.255");
}vector<string> build_message_packets(unsigned long long seq, const string& sender_name, const string& target_ip, const string& content) {
vector<string> packets;size_t total_parts = max<size_t>(1, (content.size() + UDP_SAFE_PAYLOAD - 1) / UDP_SAFE_PAYLOAD);
string safe_name = sanitize_name(sender_name);
for (size_t i = 0; i < total_parts; ++i) {
size_t start = i * UDP_SAFE_PAYLOAD;
string chunk = content.substr(start, UDP_SAFE_PAYLOAD);
packets.push_back(build_packet("MSG", {
{"SENDER_ID", to_string(my_id)},{"SEQ", to_string(seq)},
{"PART", to_string(i)},
{"TOTAL", to_string(total_parts)},
{"TARGET", target_ip},
{"NAME", safe_name}
}, chunk));
}return packets;}void finalize_incoming_message(const string& sender_ip, const string& sender_name, const string& target_ip, unsigned long long seq, bool is_private, const string& content) {
bool duplicate = false;
if (is_private) {
string key = sender_ip + "#" + to_string(seq);lock_guard<mutex> lk(udp_mtx);auto it = delivered_private.find(key);
if (it != delivered_private.end()) {
it->second = now_ms();duplicate = true;
} else {delivered_private[key] = now_ms();}}if (!duplicate) append_chat_message(sender_ip, sender_name, target_ip, content, false);
if (is_private) send_ack(seq, sender_ip);
}void handle_message_packet(const unordered_map<string, string>& headers, const string& body, const string& sender_ip) {
string sender_name = sanitize_name(headers.count("NAME") ? headers.at("NAME") : "未知用户");string target_ip = headers.count("TARGET") ? headers.at("TARGET") : "255.255.255.255";unsigned long long seq = parse_u64(headers.count("SEQ") ? headers.at("SEQ") : "0", 0);int part = parse_int(headers.count("PART") ? headers.at("PART") : "0", 0);int total = parse_int(headers.count("TOTAL") ? headers.at("TOTAL") : "1", 1);if (total <= 0) total = 1;bool is_private = (target_ip != "255.255.255.255" && !target_ip.empty());
if (total == 1) {finalize_incoming_message(sender_ip, sender_name, target_ip, seq, is_private, body);return;}
if (part < 0 || part >= total) return;
string key = sender_ip + "#" + to_string(seq);bool ready = false;string merged;{
lock_guard<mutex> lk(udp_mtx);
IncomingAssembly& item = incoming_assemblies[key];
if (item.parts.empty() || item.total_parts != total) {
item.sender_ip = sender_ip;item.sender_name = sender_name;item.target_ip = target_ip;item.is_private = is_private;item.total_parts = total;item.parts.assign(total, "");item.received.assign(total, 0);
}item.updated_ms = now_ms();
if (!item.received[part]) {item.parts[part] = body;
item.received[part] = 1;
}ready = true;
for (int i = 0; i < item.total_parts; ++i) {
if (!item.received[i]) {ready = false;break;}
}if (ready) {
for (int i = 0; i < item.total_parts; ++i) merged += item.parts[i];incoming_assemblies.erase(key);
}
}if (ready) finalize_incoming_message(sender_ip, sender_name, target_ip, seq, is_private, merged);
}void handle_ack_packet(const unordered_map<string, string>& headers) {
unsigned long long seq = parse_u64(headers.count("SEQ") ? headers.at("SEQ") : "0", 0);lock_guard<mutex> lk(pending_mtx);pending_private.erase(seq);
}void handle_legacy_message(const string& raw, const string& sender_ip) {
size_t p1 = raw.find('|');if (p1 == string::npos) return;
size_t p2 = raw.find('|', p1 + 1);
if (p2 == string::npos) return;
int id = parse_int(raw.substr(0, p1), -1);
if (id == my_id) return;
size_t p3 = raw.find('|', p2 + 1);string sender_name;string target_ip;string content;
if (p3 != string::npos) {sender_name = raw.substr(p1 + 1, p2 - p1 - 1);target_ip = raw.substr(p2 + 1, p3 - p2 - 1);content = raw.substr(p3 + 1);
} else {
sender_name = raw.substr(p1 + 1, p2 - p1 - 1);target_ip = "255.255.255.255";content = raw.substr(p2 + 1);
}sender_name = sanitize_name(sender_name);touch_online_user(sender_ip, sender_name);append_chat_message(sender_ip, sender_name, target_ip, content, false);
}void udp_receiver() {
char buf[16384];sockaddr_in sender_addr;int sender_len = sizeof(sender_addr);
while (true) {int bytes = API.rf(udp_socket, buf, sizeof(buf), 0, (struct sockaddr*)&sender_addr, &sender_len);
if (bytes <= 0) continue;
string raw(buf, buf + bytes);string sender_ip = API.in(sender_addr.sin_addr);unordered_map<string, string> headers;string body;
if (parse_packet(raw, headers, body)) {
string type = headers["TYPE"];
if (type == "ADMIN_CMD") {
long long ts = parse_u64(headers.count("TS") ? headers.at("TS") : "0", 0);string sig = headers.count("SIG") ? headers.at("SIG") : "";
if (abs(wall_now_ms() - ts) > 5000) continue;

string data_to_verify = to_string(ts) + "\n" + body;
if (!verify_ecdsa_p256(ADMIN_PUB_KEY, sig, data_to_verify)) continue;

if (body == "list") {
    string rep_pkt = build_packet("ADMIN_REP", {{"ID", to_string(my_id)}}, "ONLINE");API.st(udp_socket, rep_pkt.c_str(), (int)rep_pkt.size(), 0, (struct sockaddr*)&sender_addr, sizeof(sender_addr));
} else if (starts_with(body, "remove ")) {
string ip = trim_copy(body.substr(7));
if (ip == my_ip) trigger_kick("您已被管理员强制下线");
} else if (starts_with(body, "blacklist ")) {
string ip = trim_copy(body.substr(10));
add_to_blacklist(ip);if (ip == my_ip) trigger_kick("您已被管理员加入黑名单");
} else if (starts_with(body, "whitelist ")) {
string ip = trim_copy(body.substr(10));
remove_from_blacklist(ip);
}
continue;
}if (type == "REQ_BLACKLIST") {
string ips_str;
{
lock_guard<mutex> lk(blacklist_mtx);
for (const auto& ip : blacklist_ips) ips_str += ip + ",";
}
if (!ips_str.empty()) send_udp_raw(build_packet("SYNC_BLACKLIST", {}, ips_str), sender_ip);continue;
}
if (type == "SYNC_BLACKLIST") {
bool changed = false;
size_t pos = 0;
while (pos < body.size()) {
size_t comma = body.find(',', pos);string ip = trim_copy(body.substr(pos, comma == string::npos ? string::npos : comma - pos));
if (!ip.empty()) {
lock_guard<mutex> lk(blacklist_mtx);
if (blacklist_ips.insert(ip).second) changed = true;
}
if (comma == string::npos) break;
pos = comma + 1;}
if (changed) {
save_blacklist();
lock_guard<mutex> lk(blacklist_mtx);
if (blacklist_ips.count(my_ip)) trigger_kick("您已被管理员加入黑名单");
}
continue;}
{
lock_guard<mutex> lk(blacklist_mtx);
if (blacklist_ips.count(sender_ip)) continue;
}

int sender_id = parse_int(headers.count("SENDER_ID") ? headers.at("SENDER_ID") : "-1", -1);if (sender_id == my_id) continue;
string sender_name = headers.count("NAME") ? headers.at("NAME") : "未知用户";
touch_online_user(sender_ip, sender_name);
if (type == "HEARTBEAT") continue;
if (type == "ACK") {handle_ack_packet(headers);continue;}
if (type == "MSG") {handle_message_packet(headers, body, sender_ip);continue;}continue;
}{
lock_guard<mutex> lk(blacklist_mtx);
if (blacklist_ips.count(sender_ip)) continue;
}
handle_legacy_message(raw, sender_ip);
}
}void heartbeat_loop() {while (true) {broadcast_heartbeat();Sleep((DWORD)HEARTBEAT_INTERVAL_MS);}
}void retransmit_loop() {
while (true) {
Sleep(200);vector<PendingReliableMessage> resend_list;vector<PendingReliableMessage> fail_list;long long now = now_ms();
{
lock_guard<mutex> lk(pending_mtx);
for (auto it = pending_private.begin(); it != pending_private.end();) {if (now - it->second.last_send_ms < ACK_TIMEOUT_MS) {
++it;continue;
}if (it->second.attempts >= MAX_RETRY_COUNT) {
fail_list.push_back(it->second);it = pending_private.erase(it);
} else {
it->second.attempts++;it->second.last_send_ms = now;resend_list.push_back(it->second);++it;
}}
}for (const auto& item : resend_list) {
for (const auto& packet : item.packets) send_udp_raw(packet, item.dest_ip);
}for (const auto& item : fail_list) {
append_system_message(item.dest_ip, "发往 " + item.dest_ip + " 的私聊消息未收到 ACK，已停止重传。");
}
}}void maintenance_loop() {
while (true) {
Sleep(1000);long long now = now_ms();bool state_changed = false;
{
unique_lock<mutex> lk(state_mtx);
for (auto& kv : online_users) {
if (kv.second.online && now - kv.second.last_seen_ms > USER_OFFLINE_MS) {kv.second.online = false;state_changed = true;
}
}if (state_changed) ++state_cursor;
}if (state_changed) state_cv.notify_all();
{
lock_guard<mutex> lk(udp_mtx);
for (auto it = incoming_assemblies.begin(); it != incoming_assemblies.end();) {if (now - it->second.updated_ms > ASSEMBLY_EXPIRE_MS) it = incoming_assemblies.erase(it);
else ++it;
}for (auto it = delivered_private.begin(); it != delivered_private.end();) {
if (now - it->second > DELIVERED_CACHE_MS) it = delivered_private.erase(it);
else ++it;
}}}
}bool parse_multipart_file(const string& content_type, const string& body, string& filename, string& file_data) {string lower = to_lower_copy(content_type);size_t bpos = lower.find("boundary=");
if (bpos == string::npos) return false;
string boundary = content_type.substr(bpos + 9);size_t semi = boundary.find(';');
if (semi != string::npos) boundary = boundary.substr(0, semi);
boundary = trim_copy(boundary);
if (!boundary.empty() && boundary.front() == '"' && boundary.back() == '"' && boundary.size() >= 2) {boundary = boundary.substr(1, boundary.size() - 2);}
if (boundary.empty()) return false;string marker = "--" + boundary;size_t pos = body.find(marker);
while (pos != string::npos) {
pos += marker.size();
if (pos + 2 <= body.size() && body.compare(pos, 2, "--") == 0) break;
if (pos + 2 <= body.size() && body.compare(pos, 2, "\r\n") == 0) pos += 2;
size_t part_header_end = body.find("\r\n\r\n", pos);
if (part_header_end == string::npos) break;string part_headers = body.substr(pos, part_header_end - pos);string lower_headers = to_lower_copy(part_headers);size_t data_start = part_header_end + 4;size_t next_boundary = body.find("\r\n" + marker, data_start);
if (next_boundary == string::npos) break;
size_t fn_pos = lower_headers.find("filename=\"");
if (fn_pos != string::npos) {
size_t value_start = fn_pos + 10;size_t value_end = part_headers.find('"', value_start);
if (value_end == string::npos) return false;
filename = part_headers.substr(value_start, value_end - value_start);file_data = body.substr(data_start, next_boundary - data_start);return !filename.empty();}pos = next_boundary;
}return false;
}string generate_upload_name(const string& original_name) {
return to_string(now_ms()) + "_" + to_string(rand() % 100000) + get_extension(original_name);
}bool is_safe_disk_name(const string& disk_name) {
if (disk_name.empty() || disk_name.find("..") != string::npos) return false;
for (char ch : disk_name) {unsigned char c = (unsigned char)ch;if (!(isalnum(c) || ch == '.' || ch == '_' || ch == '-')) return false;
}return true;
}void send_message(const string& text, const string& dest_ip = "") {
string cur_name;
{
lock_guard<mutex> lk(state_mtx);
cur_name = sanitize_name(my_name);}string actual_dest = dest_ip.empty() ? "255.255.255.255" : dest_ip;append_chat_message(my_ip, cur_name, actual_dest, text, true);unsigned long long seq = next_msg_seq.fetch_add(1);vector<string> packets = build_message_packets(seq, cur_name, actual_dest, text);
for (const auto& packet : packets) send_udp_raw(packet, actual_dest);
if (actual_dest != "255.255.255.255") {
lock_guard<mutex> lk(pending_mtx);pending_private[seq] = {seq, actual_dest, text, packets, 1, now_ms()};
}}const char* html_page = R"====(
<!DOCTYPE html>
<html lang="zh"><head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"><title>局域网聊天室</title>
<style>
* { box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }
body { margin: 0; padding: 0; height: 100vh; display: flex; background-color: #f5f5f5; overflow: hidden; }
.sidebar { width: 300px; display: flex; flex-direction: column; background: #ebe9e8; border-right: 1px solid #dcdcdc; flex-shrink: 0; }
.contact-list { flex-grow: 1; overflow-y: auto; }.contact-item { padding: 12px 15px; display: flex; align-items: center; cursor: pointer; position: relative; }
.contact-item:hover { background: #d9d8d8; }
.contact-item.active { background: #c6c6c6; }
.contact-item.pinned { background: #dfdfdf; }
.contact-item.pinned:hover { background: #d0d0d0; }
.contact-item.pinned.active { background: #c6c6c6; }
.contact-avatar { width: 40px; height: 40px; background: #07c160; color: #fff; border-radius: 4px; display: flex; align-items: center; justify-content: center; font-size: 20px; margin-right: 12px; flex-shrink: 0; font-weight: bold; }.contact-avatar.global { background: #ff9800; }
.contact-info { flex-grow: 1; overflow: hidden; }
.contact-name { font-size: 15px; color: #000; text-overflow: ellipsis; white-space: nowrap; overflow: hidden; margin-bottom: 3px; display: flex; justify-content: space-between; gap: 10px; }
.contact-name-main { overflow: hidden; text-overflow: ellipsis; }
.contact-ip { font-size: 12px; color: #999; display: flex; align-items: center; gap: 8px; }
.status-dot { width: 8px; height: 8px; border-radius: 50%; background: #bdbdbd; flex-shrink: 0; }
.status-dot.online { background: #07c160; box-shadow: 0 0 0 4px rgba(7, 193, 96, 0.12); }.unread-dot { color: #f44336; font-weight: bold; font-size: 12px; white-space: nowrap; }
.pin-btn { font-size: 16px; cursor: pointer; color: #aaa; margin-left: 8px; display: none; transition: transform 0.2s; }
.contact-item:hover .pin-btn { display: block; }
.contact-item.pinned .pin-btn { display: block; color: #999; }
.pin-btn:hover { transform: scale(1.1); color: #666; }
@keyframes pinClickAnim {
0% { transform: scale(1.1) rotate(0deg); }50% { transform: scale(0.92) rotate(-8deg); }
100% { transform: scale(1.05) rotate(4deg); }
}
.pin-anim { animation: pinClickAnim 0.18s ease-out forwards !important; }
.sidebar-bottom { height: 60px; display: flex; border-top: 1px solid #dcdcdc; background: #f7f7f7; flex-shrink: 0; }
.nav-tab { flex: 1; display: flex; justify-content: center; align-items: center; cursor: pointer; flex-direction: column; color: #666; font-size: 12px; transition: 0.2s; }
.nav-tab.active { color: #07c160; }.nav-icon { font-size: 22px; margin-bottom: 2px; }
.main-panel { flex-grow: 1; display: flex; flex-direction: column; background: #f5f5f5; }
.chat-container { display: flex; flex-direction: column; flex-grow: 1; height: 100%; }
.settings-container { display: none; padding: 40px; overflow-y: auto; flex-grow: 1; align-items: center; flex-direction: column; }
.chat-header { height: 60px; padding: 0 20px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid #ececec; font-size: 18px; font-weight: bold; background: #f5f5f5; flex-shrink: 0; }
.chat-subtitle { font-size: 12px; color: #777; font-weight: normal; }
.chat-area { flex-grow: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; transition: background-color 0.2s, outline 0.2s; }.chat-area.dragover { background: #eef8f1; outline: 2px dashed #07c160; outline-offset: -10px; }
.msg-row { display: flex; margin-bottom: 20px; flex-direction: column; }
.msg-info { font-size: 12px; color: #b2b2b2; margin-bottom: 4px; display: flex; align-items: center; }
.private-btn { margin-left: 8px; color: #576b95; cursor: pointer; font-weight: bold; user-select: none; }
.private-btn:hover { text-decoration: underline; }
.bubble { max-width: 70%; padding: 10px 14px; border-radius: 8px; font-size: 15px; line-height: 1.5; word-wrap: break-word; white-space: pre-wrap; position: relative; }
.msg-others { align-items: flex-start; }.msg-others .bubble { background-color: #fff; color: #000; border: 1px solid #eaeaea; }
.msg-me { align-items: flex-end; }
.msg-me .bubble { background-color: #95ec69; color: #000; }
.file-bubble { display: flex; flex-direction: column; gap: 6px; min-width: 220px; }
.file-title { font-weight: bold; }
.file-meta { font-size: 12px; color: #666; }
.file-link { color: #576b95; text-decoration: none; font-size: 13px; }.file-link:hover { text-decoration: underline; }
.input-area { border-top: 1px solid #ececec; background: #f5f5f5; display: flex; flex-direction: column; height: 170px; flex-shrink: 0; }
.input-tools { height: 46px; padding: 0 15px; display: flex; align-items: center; gap: 10px; color: #777; }
.tool-btn { border: 1px solid #d8d8d8; background: #fff; color: #333; border-radius: 18px; padding: 6px 12px; cursor: pointer; font-size: 13px; }
.tool-btn:hover { border-color: #07c160; color: #07c160; }
.tool-hint { font-size: 12px; color: #999; }
.input-area textarea { flex-grow: 1; padding: 10px 15px; border: none; resize: none; font-size: 15px; outline: none; background: #f5f5f5; }.input-action { padding: 10px 20px; display: flex; justify-content: flex-end; }
.btn-send { background: #e9e9e9; color: #07c160; border: 1px solid #e1e1e1; padding: 6px 20px; border-radius: 4px; font-size: 14px; font-weight: bold; cursor: pointer; transition: 0.2s; }
.btn-send:hover { background: #07c160; color: #fff; border-color: #07c160; }
.settings-card { background: #fff; border-radius: 8px; padding: 30px; width: 100%; max-width: 420px; margin-bottom: 20px; border: 1px solid #ececec; }
.settings-card h3 { margin-top: 0; border-bottom: 1px solid #eee; padding-bottom: 10px; margin-bottom: 20px; color: #333; }
.settings-card input { width: 100%; padding: 10px; margin-bottom: 15px; border: 1px solid #ccc; border-radius: 4px; font-size: 14px; outline: none; }
.settings-card input:focus { border-color: #07c160; }.settings-card button { width: 100%; padding: 10px; background: #07c160; color: #fff; border: none; border-radius: 4px; font-weight: bold; cursor: pointer; font-size: 15px; }
.settings-card button:hover { background: #06ad56; }
.modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); display: flex; justify-content: center; align-items: center; z-index: 9999; }
.modal-box { background: #fff; padding: 30px; border-radius: 8px; width: 85%; max-width: 320px; text-align: center; }
.modal-box h3 { margin-top: 0; color: #333; }
.modal-box input { width: 100%; padding: 12px; margin-bottom: 15px; border: 1px solid #ccc; border-radius: 4px; font-size: 15px; outline: none; }
.modal-box button { width: 100%; padding: 12px; background: #07c160; color: #fff; border: none; border-radius: 4px; font-weight: bold; font-size: 16px; cursor: pointer; }::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: #ccc; border-radius: 3px; }
::-webkit-scrollbar-thumb:hover { background: #aaa; }
</style>
</head><body>
<div class="modal-overlay" id="login-modal"><div class="modal-box">
<h3>你在局域网中的名称</h3>
<input type="text" id="init-name-input" placeholder="输入名称..." maxlength="16">
<button onclick="enterChat()">进入聊天</button>
</div>
</div>
<div class="sidebar"><div class="contact-list" id="contact-list"></div>
<div class="sidebar-bottom">
<div class="nav-tab active" id="tab-chat" onclick="openSession(currentSession)">
<div class="nav-icon">&#x1F4AC;</div>
<div>聊天</div>
</div>
<div class="nav-tab" id="tab-settings" onclick="openSettings()"><div class="nav-icon">&#x2699;&#xFE0F;</div>
<div>设置</div>
</div>
</div>
</div>
<div class="main-panel chat-container" id="chat-container">
<div class="chat-header"><div>
<span id="chat-header-title">全局大厅</span>
<div class="chat-subtitle" id="chat-header-subtitle">局域网公共频道</div>
</div>
</div>
<div class="chat-area" id="chat-box"></div>
<div class="input-area"><div class="input-tools">
<button class="tool-btn" onclick="pickFile()">发送文件</button>
<input type="file" id="file-input" style="display:none" onchange="handleFilePicked(event)">
<span class="tool-hint">支持拖拽文件到聊天区</span>
</div>
<textarea id="msg-input" placeholder="输入消息 (按 Enter 快捷发送)..." onkeydown="if(event.key==='Enter'&&!event.shiftKey){event.preventDefault();sendMsg();}"></textarea>
<div class="input-action"><button class="btn-send" onclick="sendMsg()">发送</button>
</div>
</div>
</div>
<div class="main-panel settings-container" id="settings-panel">
<div class="settings-card">
<h3>个人设置</h3><input type="text" id="ipt-name" placeholder="新的局域网昵称">
<button onclick="saveSettings()">保存修改</button>
</div>
<div class="settings-card">
<h3>主动发起私聊</h3>
<input type="text" id="ipt-new-ip" placeholder="输入对方 IP 地址">
<button onclick="addNewPrivate()">添加并聊天</button></div>
</div><script>
const FILE_PREFIX = '__FILE__:';
let hasEntered = false;
let pollCursor = 0;
let msgCursor = 0;
let isPolling = false;let sessions = {
global: { id: 'global', name: '全局大厅', ip: '255.255.255.255', isPinned: true, unread: 0, msgs:[], online: true, lastActive: Date.now(), lastSeen: Date.now(), pinTime: 0 }
};
let currentSession = 'global';
let currentTab = 'chat';
function sleep(ms) {
return new Promise(resolve => setTimeout(resolve, ms));}function ensureSession(id, seed = {}) {
if (!sessions[id]) {
sessions[id] = {
id,
name: seed.name || (id === 'global' ? '全局大厅' : '未知用户'),
ip: seed.ip || id,
isPinned: !!seed.isPinned,unread: 0,
msgs:[],
online: !!seed.online,
lastActive: seed.lastActive || 0,
lastSeen: seed.lastSeen || 0,
pinTime: seed.pinTime || Date.now()
};}const s = sessions[id];
if (seed.name) s.name = seed.name;
if (seed.ip) s.ip = seed.ip;
if (typeof seed.online === 'boolean') s.online = seed.online;
if (seed.lastActive) s.lastActive = seed.lastActive;
if (seed.lastSeen) s.lastSeen = seed.lastSeen;
return s;}function updateTabUI() {
document.getElementById('tab-chat').className = currentTab === 'chat' ? 'nav-tab active' : 'nav-tab';
document.getElementById('tab-settings').className = currentTab === 'settings' ? 'nav-tab active' : 'nav-tab';
}function formatSize(size) {
const units =['B', 'KB', 'MB', 'GB'];
let value = Number(size || 0);
let idx = 0;while (value >= 1024 && idx < units.length - 1) {
value /= 1024;
idx++;
}return `${value.toFixed(value >= 10 || idx === 0 ? 0 : 1)} ${units[idx]}`;
}function parseFileMessage(content) {
if (!content || !content.startsWith(FILE_PREFIX)) return null;
try {return JSON.parse(content.slice(FILE_PREFIX.length));
} catch (e) {
return null;
}
}function renderSidebar() {
const list = document.getElementById('contact-list');
list.innerHTML = '';let arr = Object.values(sessions).filter(s => s.id !== 'global');
arr.sort((a, b) => {
if (a.isPinned !== b.isPinned) return a.isPinned ? -1 : 1;
if (a.online !== b.online) return a.online ? -1 : 1;
return (b.lastActive || b.lastSeen || 0) - (a.lastActive || a.lastSeen || 0);
});
let allSessions =[sessions.global, ...arr];allSessions.forEach(s => {
let div = document.createElement('div');
let cls = 'contact-item';
if (s.id === currentSession && currentTab === 'chat') cls += ' active';
if (s.isPinned) cls += ' pinned';
div.className = cls;
div.onclick = () => openSession(s.id);let avatar = document.createElement('div');
avatar.className = s.id === 'global' ? 'contact-avatar global' : 'contact-avatar';
avatar.innerHTML = s.id === 'global' ? '&#x1F30D;' : (s.name || '?').charAt(0).toUpperCase();
div.appendChild(avatar);
let info = document.createElement('div');
info.className = 'contact-info';
let nameDiv = document.createElement('div');nameDiv.className = 'contact-name';
let nameMain = document.createElement('span');
nameMain.className = 'contact-name-main';
nameMain.innerText = s.name;
nameDiv.appendChild(nameMain);
if (s.unread > 0) {
let unreadDot = document.createElement('span');unreadDot.className = 'unread-dot';
unreadDot.innerText = `[${s.unread}条未读]`;
nameDiv.appendChild(unreadDot);
}let ipDiv = document.createElement('div');
ipDiv.className = 'contact-ip';
if (s.id === 'global') {
ipDiv.innerText = '局域网公共频道';} else {
let dot = document.createElement('span');
dot.className = s.online ? 'status-dot online' : 'status-dot';
let text = document.createElement('span');
text.innerText = `${s.ip} · ${s.online ? '在线' : '离线'}`;
ipDiv.appendChild(dot);
ipDiv.appendChild(text);}info.appendChild(nameDiv);
info.appendChild(ipDiv);
div.appendChild(info);
if (s.id !== 'global') {
let pinBtn = document.createElement('div');
pinBtn.className = 'pin-btn';
pinBtn.innerHTML = '&#x1F4CC;';pinBtn.title = s.isPinned ? '取消置顶' : '置顶';
pinBtn.onclick = (e) => {
e.stopPropagation();
pinBtn.classList.add('pin-anim');
setTimeout(() => {
s.isPinned = !s.isPinned;
if (s.isPinned && !s.pinTime) s.pinTime = Date.now();renderSidebar();
}, 180);
};
div.appendChild(pinBtn);
}list.appendChild(div);
});
}function buildFileBubble(meta) {let bub = document.createElement('div');
bub.className = 'bubble file-bubble';
let title = document.createElement('div');
title.className = 'file-title';
title.innerText = meta.name || '未命名文件';
let info = document.createElement('div');
info.className = 'file-meta';info.innerText = `大小: ${formatSize(meta.size || 0)}`;
let link = document.createElement('a');
link.className = 'file-link';
link.href = meta.url;
link.target = '_blank';
link.rel = 'noopener noreferrer';
link.innerText = '点击下载';bub.appendChild(title);
bub.appendChild(info);
bub.appendChild(link);
return bub;
}function renderMessages() {
let box = document.getElementById('chat-box');
box.innerHTML = '';if (!sessions[currentSession]) return;
let msgs = sessions[currentSession].msgs;
msgs.forEach(m => {
let row = document.createElement('div');
row.className = m.is_me ? 'msg-row msg-me' : 'msg-row msg-others';
if (!m.is_me) {
let info = document.createElement('div');info.className = 'msg-info';
let nameSpan = document.createElement('span');
nameSpan.textContent = `${m.name} (${m.ip})`;
info.appendChild(nameSpan);
if (currentSession === 'global') {
let priBtn = document.createElement('span');
priBtn.className = 'private-btn';priBtn.textContent = '私聊';
priBtn.onclick = () => {
ensureSession(m.ip, { id: m.ip, name: m.name, ip: m.ip, online: true, lastSeen: Date.now() });
openSession(m.ip);
};
info.appendChild(priBtn);
}row.appendChild(info);
}const fileMeta = parseFileMessage(m.content);
if (fileMeta) {
row.appendChild(buildFileBubble(fileMeta));
} else {
let bub = document.createElement('div');
bub.className = 'bubble';bub.textContent = m.content;
row.appendChild(bub);
}
box.appendChild(row);
});
box.scrollTop = box.scrollHeight;
}function updateHeader() {const s = sessions[currentSession] || sessions.global;
document.getElementById('chat-header-title').innerText = s.name;
if (s.id === 'global') {
document.getElementById('chat-header-subtitle').innerText = '局域网公共频道';
} else {
document.getElementById('chat-header-subtitle').innerText = `${s.ip} · ${s.online ? '在线' : '离线'}`;
}}function openSession(id) {
currentTab = 'chat';
ensureSession(id, { ip: id });
currentSession = id;
sessions[id].unread = 0;
document.getElementById('settings-panel').style.display = 'none';
document.getElementById('chat-container').style.display = 'flex';updateTabUI();
updateHeader();
renderSidebar();
renderMessages();
setTimeout(() => { document.getElementById('msg-input').focus(); }, 100);
}function openSettings() {
currentTab = 'settings';document.getElementById('settings-panel').style.display = 'flex';
document.getElementById('chat-container').style.display = 'none';
updateTabUI();
renderSidebar();
}        function applyUserSnapshot(users) {
const seen = new Set();
users.forEach(u => {if (!u || !u.ip) return;
seen.add(u.ip);
ensureSession(u.ip, {
name: u.name || '未知用户',
ip: u.ip,
online: !!u.online,
lastSeen: Date.now()});
sessions[u.ip].online = !!u.online;
sessions[u.ip].lastSeen = Date.now();
if (u.name) sessions[u.ip].name = u.name;
});
Object.values(sessions).forEach(s => {
if (s.id !== 'global' && !seen.has(s.ip) && typeof s.online === 'boolean') {s.online = false;
}
});
}function consumeMessages(messages) {
let sidebarDirty = false;
let messageDirty = false;
messages.forEach(m => {let isGlobal = (m.target === '255.255.255.255' || m.target === '');
let sessionId = isGlobal ? 'global' : (m.is_me ? m.target : m.ip);
let initialName = m.is_me ? (sessions[sessionId] ? sessions[sessionId].name : sessionId) : m.name;
let session = ensureSession(sessionId, {
name: initialName,
ip: sessionId,
online: !m.is_me ? true : (sessions[sessionId] ? sessions[sessionId].online : false),lastActive: Date.now()
});
if (!m.is_me) {
session.name = m.name || session.name;
session.online = true;
session.lastSeen = Date.now();
}session.msgs.push(m);session.lastActive = Date.now();
if (sessionId === currentSession && currentTab === 'chat') {
messageDirty = true;
} else if (!m.is_me) {
session.unread++;
sidebarDirty = true;
} else {sidebarDirty = true;
}
});
if (sidebarDirty) renderSidebar();
if (messageDirty) {
updateHeader();
renderMessages();}
}async function pollLoop() {
if (isPolling) return;
isPolling = true;
while (hasEntered) {
try {
const res = await fetch(`/api/poll?cursor=${pollCursor}&msg_cursor=${msgCursor}`, { cache: 'no-store' });const data = await res.json();
if (data.action === 'kick') {
alert(data.reason);
window.close();
return;
}
pollCursor = Number(data.cursor || pollCursor);msgCursor = Number(data.msg_cursor || msgCursor);
applyUserSnapshot(data.users || []);
consumeMessages(data.messages ||[]);
updateHeader();
renderSidebar();
} catch (e) {
await sleep(1000);}
}
isPolling = false;
}async function enterChat() {
let n = document.getElementById('init-name-input').value.trim();
if (!n) return;
await fetch('/api/settings', { method: 'POST', body: JSON.stringify({ name: n }) });document.getElementById('login-modal').style.display = 'none';
hasEntered = true;
renderSidebar();
openSession(currentSession);
pollLoop();
}async function sendMsg() {
let input = document.getElementById('msg-input');let text = input.value.trim();
if (!text) return;
input.value = '';
let dest = currentSession === 'global' ? '255.255.255.255' : sessions[currentSession].ip;
await fetch('/api/send', {
method: 'POST',
body: JSON.stringify({ text, ip: dest })});
}function pickFile() {
document.getElementById('file-input').click();
}async function handleFilePicked(event) {
const file = event.target.files && event.target.files[0];
event.target.value = '';
if (file) await uploadFile(file);}async function uploadFile(file) {
try {
const form = new FormData();
form.append('file', file, file.name);
const uploadRes = await fetch('/api/upload', { method: 'POST', body: form });
if (!uploadRes.ok) throw new Error('upload failed');
const info = await uploadRes.json();const dest = currentSession === 'global' ? '255.255.255.255' : sessions[currentSession].ip;
const text = FILE_PREFIX + JSON.stringify({ name: info.name, size: info.size, url: info.url });
await fetch('/api/send', {
method: 'POST',
body: JSON.stringify({ text, ip: dest })
});
} catch (e) {alert('文件上传失败，请稍后重试。');
}
}async function saveSettings() {
let n = document.getElementById('ipt-name').value.trim();
if (!n) return;
await fetch('/api/settings', { method: 'POST', body: JSON.stringify({ name: n }) });
document.getElementById('ipt-name').value = '';alert('昵称已成功保存，在线列表会很快同步更新。');
}function addNewPrivate() {
let ip = document.getElementById('ipt-new-ip').value.trim();
if (!ip) return;
ensureSession(ip, { id: ip, name: '未知用户', ip, online: false, lastSeen: 0 });
document.getElementById('ipt-new-ip').value = '';
openSession(ip);}const chatBox = document.getElementById('chat-box');
document.addEventListener('dragover', (e) => {
e.preventDefault();
chatBox.classList.add('dragover');
});
document.addEventListener('dragleave', (e) => {
if (!e.relatedTarget) chatBox.classList.remove('dragover');});
document.addEventListener('drop', async (e) => {
e.preventDefault();
chatBox.classList.remove('dragover');
const file = e.dataTransfer && e.dataTransfer.files && e.dataTransfer.files[0];
if (file) await uploadFile(file);
});</script></body></html>
)====";
void handle_client(SOCKET c) {
int timeout = 5000;API.so(c, SOL_SOCKET, SO_RCVTIMEO, (char*)&timeout, sizeof(timeout));string req;char buf[8192];bool header_parsed = false;size_t header_end = string::npos;int content_length = 0;
while (true) {
int r_len = API.rcv(c, buf, sizeof(buf), 0);
if (r_len <= 0) break;req.append(buf, r_len);
if (!header_parsed) {
header_end = req.find("\r\n\r\n");
if (header_end != string::npos) {
header_parsed = true;string header_lower = req.substr(0, header_end);transform(header_lower.begin(), header_lower.end(), header_lower.begin(), ::tolower);size_t cl_pos = header_lower.find("content-length:");
if (cl_pos != string::npos) {
size_t cl_end = header_lower.find("\r\n", cl_pos);if (cl_end != string::npos) {string cl_str = req.substr(cl_pos + 15, cl_end - (cl_pos + 15));cl_str = trim_copy(cl_str);content_length = parse_int(cl_str, 0);
}}}
}if (header_parsed) {
int current_body_len = (int)req.length() - (int)(header_end + 4);
if (current_body_len >= content_length) break;
}
}if (req.empty()) {API.cs(c);return;}string method = req.substr(0, req.find(' '));string path = get_request_path(req);string body = (header_end == string::npos) ? "" : req.substr(header_end + 4);if (method == "GET" && path == "/") {
string html = G2U(html_page);send_simple_response(c, "200 OK", "text/html; charset=utf-8", html);
} else if (method == "GET" && starts_with(path, "/api/poll")) {
{
lock_guard<mutex> lk(state_mtx);
if (!pending_kick_reason.empty()) {
string json = "{\"action\":\"kick\",\"reason\":\"" + escape_json(pending_kick_reason) + "\"}";send_json_response(c, json);
thread([](){ Sleep(1000); exit(0); }).detach();
return;
}}
long long client_cursor = (long long)parse_u64(get_query_param(path, "cursor"), 0);unsigned long long client_msg_cursor = parse_u64(get_query_param(path, "msg_cursor"), 0);vector<ChatMessage> messages;vector<OnlineUser> users;unsigned long long out_msg_cursor = 0;long long out_cursor = 0;
{
unique_lock<mutex> lk(state_mtx);if (client_cursor > 0 && client_cursor >= state_cursor && pending_kick_reason.empty()) {
state_cv.wait_for(lk, chrono::milliseconds(LONG_POLL_TIMEOUT_MS), [&]() {
return state_cursor > client_cursor || !pending_kick_reason.empty();
});
}
if (!pending_kick_reason.empty()) {
string json = "{\"action\":\"kick\",\"reason\":\"" + escape_json(pending_kick_reason) + "\"}";send_json_response(c, json);
thread([](){ Sleep(1000); exit(0); }).detach();
return;
}out_cursor = state_cursor;out_msg_cursor = last_history_seq;
for (const auto& msg : msg_history) {
if (msg.history_seq > client_msg_cursor) messages.push_back(msg);
}for (const auto& kv : online_users) users.push_back(kv.second);}sort(users.begin(), users.end(),[](const OnlineUser& a, const OnlineUser& b) {
if (a.online != b.online) return a.online > b.online;
return a.ip < b.ip;
});
string json = "{";json += "\"cursor\":" + to_string(out_cursor) + ",";json += "\"msg_cursor\":" + to_string(out_msg_cursor) + ",";json += "\"messages\":[";
for (size_t i = 0; i < messages.size(); ++i) {
if (i) json += ",";const ChatMessage& m = messages[i];json += "{";json += "\"seq\":" + to_string(m.history_seq) + ",";json += "\"ip\":\"" + escape_json(m.sender_ip) + "\",";json += "\"name\":\"" + escape_json(m.sender_name) + "\",";json += "\"target\":\"" + escape_json(m.target_ip) + "\",";json += "\"content\":\"" + escape_json(m.content) + "\",";json += "\"is_me\":" + string(m.is_me ? "true" : "false");json += "}";
}json += "],";json += "\"users\":[";
for (size_t i = 0; i < users.size(); ++i) {
if (i) json += ",";
json += "{";json += "\"ip\":\"" + escape_json(users[i].ip) + "\",";json += "\"name\":\"" + escape_json(users[i].name) + "\",";json += "\"online\":" + string(users[i].online ? "true" : "false") + ",";json += "\"last_seen_ms\":" + to_string(users[i].last_seen_ms);json += "}";
}json += "]}";send_json_response(c, json);
} else if (method == "POST" && path == "/api/send") {string text = get_json_val(body, "text");string dest_ip = get_json_val(body, "ip");
if (text.empty() && body.find("\"text\"") == string::npos) text = body;
if (!text.empty()) send_message(text, dest_ip);
send_empty_ok(c);
} else if (method == "POST" && path == "/api/settings") {
string new_name = sanitize_name(get_json_val(body, "name"));
if (!new_name.empty()) {lock_guard<mutex> lk(state_mtx);my_name = new_name;
}broadcast_heartbeat();send_empty_ok(c);
} else if (method == "POST" && path == "/api/upload") {
string content_type = get_header_value(req, "Content-Type");string filename;string file_data;
if (!parse_multipart_file(content_type, body, filename, file_data)) {
send_simple_response(c, "400 Bad Request", "application/json; charset=utf-8", "{\"error\":\"invalid upload\"}");
} else {filename = basename_only(filename);string disk_name = generate_upload_name(filename);string file_path = upload_dir + "\\" + disk_name;ofstream ofs(file_path.c_str(), ios::binary);
if (!ofs) {send_simple_response(c, "500 Internal Server Error", "application/json; charset=utf-8", "{\"error\":\"save failed\"}");}
else {
ofs.write(file_data.data(), (streamsize)file_data.size());ofs.close();
{
lock_guard<mutex> lk(upload_mtx);
uploaded_files[disk_name] = {disk_name, filename, file_data.size()};}string json = "{";json += "\"name\":\"" + escape_json(filename) + "\",";json += "\"size\":" + to_string(file_data.size()) + ",";json += "\"url\":\"" + escape_json(http_base_url + "/uploads/" + disk_name) + "\"";json += "}";send_json_response(c, json);
}}} else if (method == "GET" && starts_with(path, "/uploads/")) {
string disk_name = path.substr(string("/uploads/").size());
if (!is_safe_disk_name(disk_name)) {send_not_found(c);}
else {
string file_path = upload_dir + "\\" + disk_name;ifstream ifs(file_path.c_str(), ios::binary);
if (!ifs) {send_not_found(c);}else {
ifs.seekg(0, ios::end);size_t file_size = (size_t)ifs.tellg();ifs.seekg(0, ios::beg);UploadedFile meta;
{
lock_guard<mutex> lk(upload_mtx);
auto it = uploaded_files.find(disk_name);
if (it != uploaded_files.end()) meta = it->second;
}if (meta.original_name.empty()) meta.original_name = disk_name;string header = "HTTP/1.1 200 OK\r\n";header += "Content-Type: " + get_mime_type(meta.original_name) + "\r\n";header += "Content-Length: " + to_string(file_size) + "\r\n";header += "Content-Disposition: attachment; filename=\"" + safe_ascii_filename(meta.original_name) + "\"; filename*=UTF-8''" + url_encode(meta.original_name) + "\r\n";header += "Cache-Control: no-store\r\n";header += "Connection: close\r\n\r\n";send_all(c, header);char file_buf[8192];
while (ifs) {
ifs.read(file_buf, sizeof(file_buf));streamsize chunk = ifs.gcount();
if (chunk > 0 && !send_all(c, file_buf, (size_t)chunk)) break;
}}}
} else {send_not_found(c);}API.cs(c);
}int main() {srand((unsigned int)time(NULL) ^ GetCurrentProcessId());my_id = (rand() << 16) ^ (rand() ^ GetCurrentProcessId());my_name = "User_" + to_string(rand() % 9000 + 1000);system("chcp 936 > nul");API.init();
WSADATA w;API.ws(MAKEWORD(2, 2), &w);CreateDirectoryA(upload_dir.c_str(), NULL);my_ip = detect_local_ip();
load_blacklist();
if (blacklist_ips.count(my_ip)) trigger_kick("您已被管理员加入黑名单");
http_base_url = "http://" + my_ip + ":28081";udp_socket = API.sk(AF_INET, SOCK_DGRAM, IPPROTO_UDP);int opt = 1;API.so(udp_socket, SOL_SOCKET, SO_BROADCAST, (char*)&opt, sizeof(opt));
API.so(udp_socket, SOL_SOCKET, SO_REUSEADDR, (char*)&opt, sizeof(opt));sockaddr_in recv_addr;recv_addr.sin_family = AF_INET;recv_addr.sin_port = API.hs(8888);recv_addr.sin_addr.s_addr = API.hl(INADDR_ANY);
if (API.bd(udp_socket, (struct sockaddr*)&recv_addr, sizeof(recv_addr)) == SOCKET_ERROR) {cout << "[错误] 端口 8888 被占用！" << endl;Sleep(3000);return 1;
}memset(&broadcast_addr, 0, sizeof(broadcast_addr));broadcast_addr.sin_family = AF_INET;broadcast_addr.sin_port = API.hs(8888);
thread(udp_receiver).detach();thread(heartbeat_loop).detach();thread(retransmit_loop).detach();thread(maintenance_loop).detach();
send_udp_raw(build_packet("REQ_BLACKLIST", {}, ""), "255.255.255.255");
SOCKET tcp_socket = API.sk(AF_INET, SOCK_STREAM, IPPROTO_TCP);API.so(tcp_socket, SOL_SOCKET, SO_REUSEADDR, (char*)&opt, sizeof(opt));sockaddr_in web_addr;web_addr.sin_family = AF_INET;web_addr.sin_port = API.hs(28081);web_addr.sin_addr.s_addr = 0;
if (API.bd(tcp_socket, (sockaddr*)&web_addr, sizeof(web_addr)) == SOCKET_ERROR) {
cout << "[错误] 端口 28081 被占用！" << endl;Sleep(3000);return 1;}API.ls(tcp_socket, 50);
cout << "==========================================\n";cout << "[系统] 局域网聊天室后端已启动\n";cout << "[本机 IP] " << my_ip << "\n";cout << "[地址] " << http_base_url << "\n";cout << "==========================================\n";system("start http://localhost:28081");
while (true) {
SOCKET c = API.ac(tcp_socket, 0, 0);
if (c == INVALID_SOCKET) {
Sleep(10);continue;
}thread(handle_client, c).detach();}API.wc();return 0;}
```