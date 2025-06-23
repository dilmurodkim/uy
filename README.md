
import logging
from aiogram import Bot, Dispatcher, types
from aiogram.types import InlineKeyboardMarkup, InlineKeyboardButton, ReplyKeyboardMarkup, KeyboardButton
from aiogram.utils import executor

API_TOKEN = "7752977498:AAFznqjVgNQjEpWJ1IUZi5NVpC8YJG8n4nE"
ADMIN_ID = 7766045121

logging.basicConfig(level=logging.INFO)

bot = Bot(token=API_TOKEN)
dp = Dispatcher(bot)

# === MAIN MENU ===
main_menu = ReplyKeyboardMarkup(resize_keyboard=True)
main_menu.add(
    KeyboardButton("📚 TOPIK 1"),
    KeyboardButton("📖 서울대 한국어 1A/1B"),
    KeyboardButton("🌤 Harflar"),
    KeyboardButton("💎 Premium darslar")
)

@dp.message_handler(commands=["start"])
async def start_handler(message: types.Message):
    await message.answer("Assalomu alaykum!\nKareys tili o'rgatadigan botga xush kelibsiz.\nQuyidagi menylardan birini tanlang:", reply_markup=main_menu)

# === Harflar Ma'lumoti ===
hangeul_letters_data = {
    "ㄱ": "talaffuz: ㄱ (g/k):\ngap boshida k\nichida g kabi\n\nmisol: 고기\ntalaffuzi: gogi\ntarjima: go‘sht",
    "ㄲ": "talaffuz: ㄲ (kk):\nkuchli k tovushi\n\nmisol: 끼다\ntalaffuzi: kkida\ntarjima: kiymoq",
    "ㄴ": "talaffuz: ㄴ (n):\ndoimo n kabi\n\nmisol: 누구\ntalaffuzi: dugu\ntarjima: kim?",
    "ㄷ": "talaffuz: ㄷ (d/t):\nboshida t\nichida d\n\nmisol: 다리\ntalaffuzi: dari\ntarjima: oyoq / ko‘prik",
    "ㄸ": "talaffuz: ㄸ (tt):\nkuchli t tovushi\n\nmisol: 땅\ntalaffuzi: ttang\ntarjima: yer",
    "ㄹ": "talaffuz: ㄹ (r/l):\nboshida r\noxirida yoki undoshdan keyin l\n\nmisol: 사람\ntalaffuzi: saram\ntarjima: inson",
    "ㅁ": "talaffuz: ㅁ (m):\nm tovushi\n\nmisol: 머리\ntalaffuzi: mori\ntarjima: bosh",
    "ㅂ": "talaffuz: ㅂ (b/p):\nboshida p\nichida b\n\nmisol: 바지\ntalaffuzi: paji\ntarjima: shim",
    "ㅃ": "talaffuz: ㅃ (pp):\nkuchli p tovushi\n\nmisol: 빵\ntalaffuzi: ppang\ntarjima: non",
    "ㅅ": "talaffuz: ㅅ (s):\ni bilan yumshoq eshitiladi\n\nmisol: 사과\ntalaffuzi: sagwa\ntarjima: olma",
    "ㅆ": "talaffuz: ㅆ (ss):\nkuchli s tovushi\n\nmisol: 쌀\ntalaffuzi: ssal\ntarjima: guruch",
    "ㅇ": "talaffuz: ㅇ (ng):\nboshida aytilmaydi\noxirida ng sifatida\n\nmisol: 아이\ntalaffuzi: ai\ntarjima: bola",
    "ㅈ": "talaffuz: ㅈ (j):\nj tovushi\n\nmisol: 자전거\ntalaffuzi: jajŏngŏ\ntarjima: velosiped",
    "ㅉ": "talaffuz: ㅉ (jj):\nkuchli j tovushi\n\nmisol: 짜다\ntalaffuzi: jjada\ntarjima: sho‘r",
    "ㅊ": "talaffuz: ㅊ (ch):\nch tovushi\n\nmisol: 친구\ntalaffuzi: chinggu\ntarjima: do‘st",
    "ㅋ": "talaffuz: ㅋ (k):\nkuchli k\n\nmisol: 코\ntalaffuzi: ko\ntarjima: burun",
    "ㅌ": "talaffuz: ㅌ (t):\nkuchli t\n\nmisol: 토끼\ntalaffuzi: tokki\ntarjima: quyon",
    "ㅍ": "talaffuz: ㅍ (p):\nkuchli p\n\nmisol: 포도\ntalaffuzi: podo\ntarjima: uzum",
    "ㅎ": "talaffuz: ㅎ (h):\nh tovushi\n\nmisol: 하나\ntalaffuzi: hana\ntarjima: bir",
    "ㅏ": "talaffuz: ㅏ (a):\nog‘iz katta ochiladi\n\nmisol: 아빠\ntalaffuzi: appa\ntarjima: dada",
    "ㅐ": "talaffuz: ㅐ (ae):\ne ga o‘xshash\n\nmisol: 개\ntalaffuzi: ke\ntarjima: it",
    "ㅑ": "talaffuz: ㅑ (ya):\nya tovushi\n\nmisol: 야채\ntalaffuzi: yachae\ntarjima: sabzavot",
    "ㅒ": "talaffuz: ㅒ (yae):\nya + e\n\nmisol: 얘기\ntalaffuzi: yaegi\ntarjima: suhbat",
    "ㅓ": "talaffuz: ㅓ (eo):\no ga o‘xshash\norqadan chiqadi\n\nmisol: 어머니\ntalaffuzi: ŏmŏni\ntarjima: ona",
    "ㅔ": "talaffuz: ㅔ (e):\ninglizcha e kabi\n\nmisol: 네\ntalaffuzi: ne\ntarjima: ha",
    "ㅕ": "talaffuz: ㅕ (yeo):\nyo ga o‘xshash\n\nmisol: 여자\ntalaffuzi: yŏja\ntarjima: ayol",
    "ㅖ": "talaffuz: ㅖ (ye):\nye tovushi\n\nmisol: 예\ntalaffuzi: ye\ntarjima: ha (hurmatli)",
    "ㅗ": "talaffuz: ㅗ (o):\nyuqoriga qarab o\n\nmisol: 오이\ntalaffuzi: oi\ntarjima: bodring",
    "ㅘ": "talaffuz: ㅘ (wa):\no + a\n\nmisol: 사과\ntalaffuzi: sagwa\ntarjima: olma",
    "ㅙ": "talaffuz: ㅙ (wae):\no + ae\n\nmisol: 왜\ntalaffuzi: wae\ntarjima: nega",
    "ㅚ": "talaffuz: ㅚ (oe):\nwe yoki ö kabi\n\nmisol: 외국\ntalaffuzi: oeguk\ntarjima: chet el",
    "ㅛ": "talaffuz: ㅛ (yo):\nyuqoriga qarab yo\n\nmisol: 요리\ntalaffuzi: yori\ntarjima: taom",
    "ㅜ": "talaffuz: ㅜ (u):\npastga qarab u\n\nmisol: 우유\ntalaffuzi: uyu\ntarjima: sut",
    "ㅝ": "talaffuz: ㅝ (wo):\nu + eo\n\nmisol: 워터\ntalaffuzi: wŏtŏ\ntarjima: suv",
    "ㅞ": "talaffuz: ㅞ (we):\nu + e\n\nmisol: 웨딩\ntalaffuzi: weding\ntarjima: to‘y",
    "ㅟ": "talaffuz: ㅟ (wi):\nu + i\n\nmisol: 위\ntalaffuzi: wi\ntarjima: usti","ㅠ": "talaffuz: ㅠ (yau):\npastga qarab yu\n\nmisol: 유리\ntalaffuzi: yuri\ntarjima: oynavand",
    "ㅡ": "talaffuz: ㅡ (eu):\nog‘iz tekis\n\nmisol: 으깨다\ntalaffuzi: ukkæda\ntarjima: ezmoq",
    "ㅢ": "talaffuz: ㅢ (ui):\neu + i\n\nmisol: 의사\ntalaffuzi: ŭisa\ntarjima: shifokor",
    "ㅣ": "talaffuz: ㅣ (i):\ni tovushi\n\nmisol: 이름\ntalaffuzi: irŭm\ntarjima: ism"
}

@dp.message_handler(lambda message: message.text == "🌤 Harflar")
async def show_letter_menu(message: types.Message):
    markup = InlineKeyboardMarkup(row_width=4)
    for harf in hangeul_letters_data.keys():
        markup.insert(InlineKeyboardButton(harf, callback_data=f"harf_{harf}"))
    # Orqaga tugma
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="back_to_main"))
    await message.answer("Agar siz chuqurroq o'rganishni xoxlasangiz PREMIUM sotib olishni tavsiya qilaman.\nQuyidagilardan birini tanlang:", reply_markup=markup)

@dp.callback_query_handler(lambda c: c.data.startswith("harf_"))
async def show_letter_info(callback: types.CallbackQuery):
    harf = callback.data.replace("harf_", "")
    matn = hangeul_letters_data.get(harf, "Ma’lumot topilmadi")
    # Orqaga tugma qo'shamiz
    markup = InlineKeyboardMarkup()
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="back_to_letters"))
    await callback.message.edit_text(f"🌤 {harf}\n{matn}", reply_markup=markup)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data == "back_to_letters")
async def back_to_letters(callback: types.CallbackQuery):
    markup = InlineKeyboardMarkup(row_width=4)
    for harf in hangeul_letters_data.keys():
        markup.insert(InlineKeyboardButton(harf, callback_data=f"harf_{harf}"))
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="back_to_main"))
    await callback.message.edit_text("Agar siz chuqurroq o'rganishni xoxlasangiz PREMIUM sotib olishni tavsiya qilaman.\nQuyidagilardan birini tanlang:", reply_markup=markup)
    await callback.answer()

# === Grammatika Ma'lumotlari ===
grammar_1A = {
    "1A_1": "N+은/는::Urg'u va yuklama vafifasini bajaradi...",
    "1A_2": "N+입니까? / N+입니다::So‘roq va darak gap tuzishda ishlatiladi.\nMasalan: 학생입니까?\nO'quvchimi?\n네, 학생입니다.\nHa o'quvchidir.",
    "1A_3": "N+이/가 아닙니다::Inkor shaklida ishlatiladi\n'이 아닙니다' Undosh bilan tugasa\n '가 아닙니다'Unli bilan tugasa\n Masalan:저는 학생이 아닙니다.\n Men o'quvchi emasman.",
    "1A_4": "N+이/가 있어요 / 없어요::Bor / yo‘qni bildiradi.\nMasalan: 책이 있어요.\nKitob bor.\n컴퓨터가 없어요.\nKompyuter yo'q.",
    "1A_5": "이것은 / 그것은 / 저것은::Bu / u / anavi narsani bildiradi. Masalan: 이것은 책입니다.\nBu narsa kitob dir.",
    "1A_6": "주세요 ::Biror narsani iltimos qilish. Masalan: 물 좀 주세요.\nBiroz suv bering.",
    "1A_7": "N+하고 / 과/와 / (이)랑::\"...bilan\" ma'nosini beradi. Masalan: 친구하고 같이 갔어요.\nDo'stim bilan birga bordik.",
    "1A_8": "A/V + 아요 / 어요 / 해요::Fe’l yoki sifatga qo‘shilib, hozirgi yoki hozirgi zamonga yaqin ish-harakatni bildiradi.\n\n✅ Qoidalar:\n1) ㅏ/ㅗ bilan tugasa → 아요: 가다 → 가요 (boraman)\n2) boshqa unlilar bilan → 어요: 먹다 → 먹어요 (yeyman)\n3) 하다 bilan → 해요: 공부하다 → 공부해요 (dars qilaman)\n\n📌 Misollar:\n- 가요 (boraman)\n- 와요 (kelaman)\n- 먹어요 (yeyman)\n- 공부해요 (dars qilaman)",
    "1A_9": "N+을/를::-ni qo'shimchasi\nMasalan: 밥을 먹어요.\nOvqatNI yeyapman.",
    "1A_10": "N+에서::Harakat yoki holat sodir bo‘ladigan joylarga ishlatiladi. Masalan: 집에서 공부해요.\nUyda o'qiyapman.",
    "1A_11": "안 A/V::tarjimasi:-mayman\n-maydi\nInkor shakli. Masalan: 안 가요,\nBormayman\n안 먹어요.\nYemayman.",
    "1A_12": "N+에 있어요 / 없어요::Joy nomlariga nisbatan\nTarjimasi:-da bor\n-da yo'q\nMasalan: 교실에 책이 있어요.\nSinfxonada kitob bor.",
    "1A_13": "N+에 가요 / 와요::Yo‘nalish bildiradi.\nTarjimasi:-ga bormoq\n-dan kelmoq\nMasalan: 학교에 가요.\nMaktabga borayapman.",
    "1A_14": "앞 / 옆 / 뒤::Joylashuvlarga nisbatan ishlatiladi.\nTarjimasi:oldi, yon, orqa. Masalan: 집 앞에 있어요.\nUy yonida bor.",
    "1A_15": "요일::Haftaning kunlari: 월요일 (Dushanba),\n화요일 (Seshanba),\n수요일 (Chorshanba),\n목요일 (Payshanba),\n금요일 (Juma)\n토요일 (Shanba)\n일요일 (Yakshanba)",
    "1A_16": "N+에::Vaqt yoki joy bildiradi.\nTarjimasi:-da yoki -ga\nMasalan: 오전 9시에 학교에 가요.\nErtalab 9soatda maktabga boraman.",
    "1A_17": "A/V + 았/었/했어요::O‘tgan zamon.\n\n✅ Qoidalar:\n1) Fe’lning oxirgi bo‘g‘inida ㅏ yoki ㅗ bo‘lsa → 았어요: 가다 → 갔어요 (bordim)\n2) Boshqa unlilar bo‘lsa → 었어요: 먹다 → 먹었어요 (yedim)\n3) 하다 fe’li → 했어요: 공부하다 → 공부했어요 (o‘qidim)\n\n📌 Misollar:\n- 갔어요 (bordim)\n- 왔어요 (keldim)\n- 먹었어요 (yedim)\n- 공부했어요 (o‘qidim)",
    "1A_18": "A/V + 지만::Qarama-qarshi fikr.\nTarjimasi:ammo yoki lekin\nMasalan: 맛있지만 비싸요,\nMazzali lekin qimmat.",
    "1A_19": "V + 고::Ikki harakatlarni bir-biriga bog‘laydi.\nTarjimasi:-b yoki -ib \nMasalan: 밥을 먹고 공부해요,\nOvqatni yeb tahsil olyapman.",
    "1A_20": "V + (으)세요::Hurmat shakli.\nTarjimasi:-ing\nMasalan: 들어오세요,\nKiring.",
    "1A_21": "A/V + ㅂ/습니까?, ㅂ/습니다::Rasmiy so‘zlashuv uslubi. Masalan: 갑니까?\nBorasizmi,\n갑니다,\nBoraman.",
    "1A_22": "V + 을/ㄹ까요?::Taklif yoki mulohaza.\nTarjimasi:-mizmi\nMasalan: 갈까요?\nboramizmi.",
    "1A_23": "이/그/저::Ko‘rsatish olmoshlari: bu, u, anavi. Masalan: 이 사람,\nBu odam.",
    "1A_24": "A/V + 네요::Tarjimasi: ekan\nHayronlanish, ajablanish bildiradi. Masalan: 날씨가 좋네요!. Havo yaxshi ekan!"
}

grammar_1B = {
    "1B_1": "N+(의)::-ning ma'nosini beradi.\nMisol: 친구의 책 — do'stning kitobi",
    "1B_2": "N+을/를::-ni qo‘shimchasi (obyekt).\nMisol: 밥을 먹어요 — Ovqatni yeyapman",
    "1B_3": "N+(이)세요::Hurmat shakli '...lar'.\nMisol: 선생님이세요 — Ustozlar",
    "1B_4": "V+(으)시::Hurmat ifodasi.\nMisol: 가시다 — Boradilar (hurmat bilan)",
    "1B_5": "N+부터, 까지::'dan ... gacha'.\nMisol: 아침부터 저녁까지 — Ertalabdan kechgacha",
    "1B_6": "V+아서/어서::-ib, bo‘lib.\nMisol: 공부해서 피곤해요 — O'qib charchadim",
    "1B_7": "V+(으)ㄹ 거예요::Kelasi zamon.\nMisol: 갈 거예요 — Boraman",
    "1B_8": "V+지 마세요::...mang (taqiqlov).\nMisol: 가지 마세요 — Bormang",
    "1B_9": "N+만::Faqat.\nMisol: 물만 마셔요 — Faqat suv ichaman",
    "1B_10": "V+아/어야 되다::...qilish kerak.\nMisol: 공부해야 돼요 — Dars qilish kerak",
    "1B_11": "V+아요/어요/지요?::So‘roq shakli.\nMisol: 맛있지요? — Mazalimi?",
    "1B_12": "V+고 있다::Hozirgi davomiy holat.\nMisol: 먹고 있어요 — Yeyayapman",
    "1B_13": "V+못::Qila olmaslik.\nMisol: 못 가요 — Bormayman (eplay olmayman)",
    "1B_14": "A/V+아서/어서::...ligi uchun.\nMisol: 예뻐서 좋아요 — Chiroyli bo‘lgani uchun yoqadi",
    "1B_15": "V+(으)려고 하다::...moqchi bo‘lmoq.\nMisol: 가려고 해요 — Borishni niyat qilayapman",
    "1B_16": "V+아/어 주다::...ib bering.\nMisol: 도와주세요 — Yordam bering",
    "1B_17": "(으)N+로::...ga, orqali.\nMisol: 버스로 가요 — Avtobus bilan boraman",
    "1B_18": "(으)N+L::Ot yasovchi qo‘shimcha.\nMisol: 공부한 사람 — O‘qigan odam",
    "1B_19": "N+한테 / 께::Shaxsga (hurmatli).\nMisol: 선생님께 드려요 — Ustozga beraman",
    "1B_20": "V+아/어 보세요::...ib ko‘ring.\nMisol: 입어 보세요 — Kiyib ko‘ring",
    "1B_21": "A/V+(으)면::...sa, agar.\nMisol: 비가 오면 안 가요 — Yomg‘ir yog‘sa bormayman",
    "1B_22": "V+는::Ot yasovchi zamon qo‘shimchasi.\nMisol: 먹는 사람 — Yeyayotgan odam",
    "1B_23": "V+고 싶다 (1-shaxs)::...moqchiman.\nMisol: 한국에 가고 싶어요 — Koreyaga bormoqchiman",
    "1B_24": "V+고 싶어 하다 (3-shaxs)::...moqchi (u).\nMisol: 동생이 먹고 싶어 해요 — Ukam yegisi kelayapti",
    "1B_25": "V+(으)ㄹ 수 있다 / 없다::Qila olish / qilolmaslik.\nMisol: 수영할 수 있어요 — Suzishni bilaman",
    "1B_26": "V+(으)러 가다 / 오다::...gani bormoq / kelmoq.\nMisol: 공부하러 가요 — O‘qigani boraman",
    "1B_27": "V+(으)면서::...ib, bir vaqtda.\nMisol: 음악을 들으면서 공부해요 — Musiqa eshitib dars qilaman"
}

@dp.message_handler(lambda message: message.text == "📖 서울대 한국어 1A/1B")
async def show_books(message: types.Message):
    markup = InlineKeyboardMarkup(row_width=2)
    markup.add(
        InlineKeyboardButton("1A 📚", callback_data="book_1A"),
        InlineKeyboardButton("1B 📖", callback_data="book_1B"),
        InlineKeyboardButton("⬅️ Orqaga", callback_data="back_to_main")
    )
    await message.answer("Agar siz chuqurroq o'rganishni xoxlasangiz PREMIUM sotib olishni tavsiya qilaman.\nSizga qaysi kitob kerak:", reply_markup=markup)

@dp.callback_query_handler(lambda c: c.data == "back_to_main")
async def back_to_main(callback: types.CallbackQuery):
    await callback.message.edit_text("Agar siz chuqurroq o'rganishni xoxlasangiz PREMIUM sotib olishni tavsiya qilaman.\nAssalomu alaykum! Tanlang:", reply_markup=main_menu)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data == "book_1A")
async def show_1a_menu(callback: types.CallbackQuery):
    markup = InlineKeyboardMarkup(row_width=1)
    for key in grammar_1A.keys():
        markup.add(InlineKeyboardButton(key, callback_data=f"grammar_1A_{key}"))
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="show_books_menu"))
    await callback.message.edit_text("1A grammatikalaridan birini tanlang:", reply_markup=markup)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data == "book_1B")
async def show_1b_menu(callback: types.CallbackQuery):
    markup = InlineKeyboardMarkup(row_width=1)
    for key in grammar_1B.keys():
        markup.add(InlineKeyboardButton(key, callback_data=f"grammar_1B_{key}"))
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="show_books_menu"))
    await callback.message.edit_text("1B grammatikalaridan birini tanlang:", reply_markup=markup)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data == "show_books_menu")
async def show_books_menu(callback: types.CallbackQuery):
    markup = InlineKeyboardMarkup(row_width=2)
    markup.add(
        InlineKeyboardButton("1A 📚", callback_data="book_1A"),
        InlineKeyboardButton("1B 📖", callback_data="book_1B"),
        InlineKeyboardButton("⬅️ Orqaga", callback_data="back_to_main")
    )
    await callback.message.edit_text("Agar siz chuqurroq o'rganishni xoxlasangiz PREMIUM sotib olishni tavsiya qilaman.\nSizga qaysi kitob kerak:", reply_markup=markup)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data.startswith("grammar_1A_"))
async def show_1a_grammar(callback: types.CallbackQuery):
    key = callback.data.replace("grammar_1A_", "")
    text = grammar_1A.get(key, "Ma'lumot topilmadi")
    markup = InlineKeyboardMarkup()
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="book_1A"))
    await callback.message.edit_text(f"📘 {text}", reply_markup=markup)
    await callback.answer()

@dp.callback_query_handler(lambda c: c.data.startswith("grammar_1B_"))
async def show_1b_grammar(callback: types.CallbackQuery):
    key = callback.data.replace("grammar_1B_", "")
    text = grammar_1B.get(key, "Ma'lumot topilmadi")
    markup = InlineKeyboardMarkup()
    markup.add(InlineKeyboardButton("⬅️ Orqaga", callback_data="book_1B"))
    await callback.message.edit_text(f"📗 {text}", reply_markup=markup)
    await callback.answer()

# === Premium Darslar ===
@dp.message_handler(lambda msg: msg.text == "💎 Premium darslar")
async def premium_info(msg: types.Message):
    await msg.answer(
        "💎 <b>PREMIUM DARS TARIFI</b>\n\n"
        "📌 <b>Imkoniyatlar:</b>\n"
        "🔹 Har ikki kunda jonli dars\n"
        "🔹 Faqat premiumlar uchun yopiq materiallar\n"
        "🔹 So‘z boyligi, Kareys tili 0dan o'rganish\n"
        "🔹 Savol-javoblar uchun yopiq guruh\n\n"
        "💰 <b>Narxi:</b> <u>30 000 so‘m (oyiga)</u>\n"
        "💳 <b>To‘lov uchun karta:</b>\n<code>5614 6818 1030 9850</code>\n\n"
        "📅 <b>To‘lov cheki surati bilan birga PREMIUM so'zini qoldiring!</b>",
        parse_mode="HTML"
    )

@dp.message_handler(content_types=types.ContentType.PHOTO)
async def handle_photo(message: types.Message):
    if message.caption and "premium" in message.caption.lower():
        await message.reply("✅ Chek qabul qilindi! Admin tasdig‘ini kuting!")
        await bot.send_photo(
            chat_id=ADMIN_ID,
            photo=message.photo[-1].file_id,
            caption=f"🧾 Yangi to‘lov cheki\n👤 Foydalanuvchi: @{message.from_user.username}\n🆔 ID: {message.from_user.id}\n\n✅ /premium_{message.from_user.id} orqali tasdiqlang",
        )

@dp.message_handler(lambda m: m.text and m.text.startswith("/premium_"))
async def approve_user(message: types.Message):
    if message.from_user.id != ADMIN_ID:
        return
    user_id = message.text.split("_")[-1]
    try:
        await bot.send_message(int(user_id), "🎉 Tabriklaymiz! Siz premium guruhga qo‘shilishingiz mumkin! https://t.me/+KZibbo1LW5xlYzcy")
        await message.reply("✅ Foydalanuvchi muvaffaqiyatli tasdiqlandi.")
    except Exception as e:
        await message.reply(f"❌ Xatolik: {e}")

# === TOPIK 1 ===
@dp.message_handler(lambda message: message.text == "📚 TOPIK 1")
async def topik_handler(message: types.Message):
    await message.answer("📚 TOPIK testlari tez orada qo'shiladi, qo'shilguncha shu kanaldan o'rganib turing!\nTOPIK 1 kanali: [Kanalga oʼtish](https://t.me/koreystili_birgalikda)", parse_mode="Markdown")

if __name__ == '__main__':
    executor.start_polling(dp, skip_updates=True)
