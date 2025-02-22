# Alassdy-
زئير الأسد 
إليك مجمل الأوامر البرمجية لتطبيق المحفظة الإلكترونية التي قمت بإنشائها. يمكنك نسخها ولصقها في بيئة تطويرك (مثل Replit أو Google Colab أو أي محرر آخر تفضله).

import sqlite3
from hashlib import sha256

# إنشاء قاعدة البيانات
conn = sqlite3.connect('alghiyath_wallet.db')
cursor = conn.cursor()

# إنشاء جدول للمستخدمين
cursor.execute('''
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT UNIQUE,
        password TEXT,
        balance REAL DEFAULT 0
    )
''')

# إنشاء جدول للمعاملات
cursor.execute('''
    CREATE TABLE IF NOT EXISTS transactions (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        user_id INTEGER,
        type TEXT,
        amount REAL,
        timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
        FOREIGN KEY(user_id) REFERENCES users(id)
    )
''')

conn.commit()

# دالة لتسجيل مستخدم جديد
def register(username, password):
    # تشفير كلمة المرور
    password_hash = sha256(password.encode()).hexdigest()
    try:
        cursor.execute('INSERT INTO users (username, password) VALUES (?, ?)', (username, password_hash))
        conn.commit()
        print(f"تم تسجيل المستخدم {username} بنجاح.")
    except sqlite3.IntegrityError:
        print("اسم المستخدم موجود بالفعل.")

# دالة لتسجيل الدخول
def login(username, password):
    password_hash = sha256(password.encode()).hexdigest()
    cursor.execute('SELECT id, balance FROM users WHERE username = ? AND password = ?', (username, password_hash))
    user = cursor.fetchone()
    if user:
        print(f"مرحباً {username}، تم تسجيل الدخول بنجاح.")
        return user[0], user[1]  # إرجاع معرف المستخدم والرصيد
    else:
        print("اسم المستخدم أو كلمة المرور غير صحيحة.")
        return None

# دالة لإضافة الأموال إلى المحفظة
def deposit(user_id, amount):
    cursor.execute('UPDATE users SET balance = balance + ? WHERE id = ?', (amount, user_id))
    cursor.execute('INSERT INTO transactions (user_id, type, amount) VALUES (?, ?, ?)', (user_id, 'deposit', amount))
    conn.commit()
    print(f"تم إضافة {amount} إلى رصيدك.")

# دالة لسحب الأموال
def withdraw(user_id, amount):
    cursor.execute('SELECT balance FROM users WHERE id = ?', (user_id,))
    balance = cursor.fetchone()[0]
    if balance >= amount:
        cursor.execute('UPDATE users SET balance = balance - ? WHERE id = ?', (amount, user_id))
        cursor.execute('INSERT INTO transactions (user_id, type, amount) VALUES (?, ?, ?)', (user_id, 'withdraw', amount))
        conn.commit()
        print(f"تم سحب {amount} من رصيدك.")
    else:
        print("الرصيد غير كافٍ لإتمام العملية.")

# دالة لعرض الرصيد
def check_balance(user_id):
    cursor.execute('SELECT balance FROM users WHERE id = ?', (user_id,))
    balance = cursor.fetchone()[0]
    print(f"رصيدك الحالي هو: {balance}")
    return balance

# دالة لعرض سجل المعاملات
def view_transactions(user_id):
    cursor.execute('SELECT type, amount, timestamp FROM transactions WHERE user_id = ? ORDER BY timestamp DESC', (user_id,))
    transactions = cursor.fetchall()
    if transactions:
        print("سجل المعاملات:")
        for transaction in transactions:
            print(f"{transaction[2]}: {transaction[0]} {transaction[1]}")
    else:
        print("لا توجد معاملات.")

# مثال على كيفية استخدام النظام:
# تعليق استدعاءات الدوال لتجنب التنفيذ الفعلي
# register('user1', 'password123')
# user_id, balance = login('user1', 'password123')
# deposit(user_id, 100)
# withdraw(user_id, 50)
# check_balance(user_id)
# view_transactions(user_id)

conn.close()

كيفية الاستخدام:

1. انسخ الكود إلى بيئة التطوير الخاصة بك.


2. قم بتشغيله. يجب أن يتم إنشاء قاعدة البيانات والجداول المطلوبة تلقائيًا.


3. يمكنك فك تعليق استدعاءات الدوال في الأسفل لاختبار وظائف التطبيق.



إذا كنت بحاجة إلى أي مساعدة إضافية أو توضيحات، فلا تتردد في طرحها!

