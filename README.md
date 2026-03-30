import random
import string

def is_valid_username(name, allowed_letters, want_dot, want_underscore, want_numbers, base_length):
    allowed_chars = set(allowed_letters + string.digits + "._")

    if any(c not in allowed_chars for c in name):
        return False

    if not want_dot and "." in name:
        return False

    if not want_underscore and "_" in name:
        return False

    has_numbers = any(c.isdigit() for c in name)
    if want_numbers != has_numbers:
        return False

    base = name
    if "." in base:
        base = base.replace(".", "")
    if "_" in base:
        base = base.replace("_", "")
    if want_numbers:
        base = "".join(c for c in base if not c.isdigit())

    if len(base) < base_length:
        return False

    return True


def generate_username(letters, base_length=4, use_dot=False, use_underscore=False, use_numbers=False):
    name = "".join(random.choice(letters) for _ in range(base_length))

    sep = ""
    if use_dot and use_underscore:
        sep = random.choice([".", "_", ""])
    elif use_dot:
        sep = random.choice(["", "."])
    elif use_underscore:
        sep = random.choice(["", "_"])

    if sep:
        extra_len = random.randint(1, 2)
        extra = "".join(random.choice(letters) for _ in range(extra_len))
        name += sep + extra

    if use_numbers:
        name += str(random.randint(0, 99)).zfill(2)

    return name


def generate_checked_usernames(
    count=50,
    letters="abcdefghijklmnopqrstuvwxyz",
    base_length=4,
    use_dot=False,
    use_underscore=False,
    use_numbers=False
):
    results = set()

    while len(results) < count:
        name = generate_username(
            letters=letters,
            base_length=base_length,
            use_dot=use_dot,
            use_underscore=use_underscore,
            use_numbers=use_numbers
        )

        if is_valid_username(
            name=name,
            allowed_letters=letters,
            want_dot=use_dot,
            want_underscore=use_underscore,
            want_numbers=use_numbers,
            base_length=base_length
        ):
            results.add(name)

    return sorted(results)


if __name__ == "__main__":
    count = int(input("كم يوزر تبي؟: ") or "20")
    letters = input("وش الحروف المسموحة؟ (مثال abcxyz): ").strip().lower() or string.ascii_lowercase
    base_length = int(input("كم حرف أساسي؟ (مثال 4): ") or "4")
    use_dot = input("تبي نقطة؟ (y/n): ").strip().lower() == "y"
    use_underscore = input("تبي underscore؟ (y/n): ").strip().lower() == "y"
    use_numbers = input("تبي أرقام؟ (y/n): ").strip().lower() == "y"

    usernames = generate_checked_usernames(
        count=count,
        letters=letters,
        base_length=base_length,
        use_dot=use_dot,
        use_underscore=use_underscore,
        use_numbers=use_numbers
    )

    print("\nالنتائج:\n")
    for u in usernames:
        print(u)

    with open("usernames.txt", "w", encoding="utf-8") as f:
        for u in usernames:
            f.write(u + "\n")

    print("\nتم حفظ اليوزرات في ملف usernames.txt")
