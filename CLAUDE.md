# CLAUDE.md

## Proje

Watchover — AO Hackathon 2026 Enterprise katılımı. Operasyonel gürültü → sinyal → gerekçeli incident → takip edilen aksiyon. Web uygulaması üzerinden canlı demo; çalışma zamanında LLM/API yok sayılır, çekirdek deterministiktir.

## Dizin Yapısı

- `app.py` — Streamlit arayüzü (Operasyon, Datasets, Playbook, ITSM, Connection Settings)
- `src/watchover/` — motor (loader, format_detector, parsers/, normalize, analysis, live, itsm, connectors, llm, playbook, actions, cli); case'e özel kod **sadece** `src/watchover/scenario/`
- `agent.py`, `mcp_server.py` — uzak ajan ve MCP sunucusu
- `tests/` — pytest; `samples/` — demo veri seti üretici
- `docs/` — plan, fazlar, mimari, AI_LOG
- `prompts/` — kullanılan kritik prompt'lar
- `demo/` — ekran görüntüleri ve video linki
- `AI_JURI.md`, `submission.json` — jüri özeti ve teslim künyesi

## Komutlar

```bash
# install: pip install -e ".[dev,mcp]"
# run:     streamlit run app.py          (make run)
# test:    python -m pytest -q           (make test)
# demo:    make demo                     (demo veri setini üret + çalıştır)
# mcp:     python mcp_server.py --http --port 8765
```

## Kod Kuralları

- Stack kullanıcının seçimi, değiştirme: Python 3.9+, Streamlit, pandas, python-dateutil, pytest.
- Motor `src/watchover/` altında; senaryoya özel ayar yalnız `scenario/`.
- Her adım ayrı commit, `make test` yeşil; commit mesajı: kısa başlık + madde listesi.
- Branch: `claude/cool-tesla-dh9nw3`.

## Çalışma Kuralları

1. **Token tasarrufu.** Kodu her prompt'ta baştan sona tekrar okuma. Sadece
   değişecek dosyanın ilgili bölümünü oku, arama için Grep/Glob kullan,
   gereksiz açıklama ve tekrar üretme. Kısa ve net yanıt ver.
2. **AI araç kaydı.** Hackathon "AI kullanım becerisi"ni puanlıyor ve şahsi
   araçlar (Claude SAKA ve Codex dışındakiler) katılımcı sorumluluğunda
   serbest. Bu yüzden her geliştirme adımında hangi aracın (ve versiyonunun)
   hangi iş için kullanıldığı kayıt altına alınır. İki kayıt yeri:
   (a) README.md modül tablosu ("AI aracı" sütunu): dosya bazında;
   (b) `docs/AI_LOG.md`: **her geliştirme adımı için bir satır**, sadece üç
   sütun: araç + sürüm, tarih, yapılan düzeltme. Sohbet, yönlendirme veya
   iptal edilen denemeler yazılmaz. Her commit'ten önce satır eklenir.
   Bu oturumdaki model: claude-fable-5-1 (Cowork).
3. **Tek README, artımlı güncelleme.** Repoda tek bir `README.md` olur
   (docs/ altındaki plan, fazlar, mimari ve AI_LOG ile AI_JURI.md ayrı amaçlı yardımcı dosyalardır). Her
   dosya/modül için şu bilgiler tutulur: ne işe yarar, hangi AI
   aracı/versiyonu ile üretildi, ilgili kod nasıl çalışır. Her geliştirme
   sonrası README'nin sadece ilgili bölümü güncellenir; README asla baştan
   yazılmaz (Edit ile bölüm ekle/değiştir).

## Yapma

- `.env` dosyasını ASLA commit etme (`.env.example` kullan); `*.db` ve `data/live/` de commit edilmez.
- README'yi baştan yazma; sadece ilgili bölümü güncelle.
- AI_LOG tarihlerini ve kayıtlarını gerçekte olduğundan farklı yazma.

## Notlar

- Etkinlik: AI Hackathon TR 2026. Senaryo ve veri seti
  etkinlik günü verilir, 3 saat kodlama, 17:30 hard stop, 7+3 dk sunum.
- Ürün: operasyonel gürültü -> sinyal -> gerekçeli incident -> aksiyon
  takibi. Web uygulaması üzerinden canlı demo. Çalışma zamanında LLM/API
  yok sayılır, çekirdek deterministiktir.
- Stack (kullanıcının seçimi, değiştirme): Python 3.11, Streamlit UI (`app.py`),
  pandas, stdlib csv/json, python-dateutil, pytest. Motor `src/watchover/`,
  case'e özel kod sadece `src/watchover/scenario/`. LLM opsiyonel, çekirdeğe bağlı değil.
- Branch: `claude/cool-tesla-dh9nw3`. Her adım ayrı commit, `make test` yeşil.

## Güvenlik denetimi (security-audit)

Bu depoda Cloudflare'in `security-audit` skill'i bulunur: `.claude/skills/security-audit/` (kaynak: github.com/cloudflare/security-audit-skill, commit c1c8a8c; MIT lisansı aynı klasörde).

- **Ne zaman:** bu depoya yapılan her push'tan sonra ve her sürüm / yama yayınından önce.
- **Nasıl:** skill'i tam denetim modunda çalıştır. Hedef bu depodur; çıktı dizini depo dışında olur (`~/security-audit-skill/<depo>/run-<N>`). Önceki çalıştırmaların `coverage-ledger.json` ve `findings.json` dosyaları varsa onları kullanıp değişen kodu öncelikli denetle.
- **Güvenlik kuralı:** hedef kod yalnız işletim sistemi seviyesinde yalıtılmış bir ortamda çalıştırılır; böyle bir ortam yoksa bulgu `needs_validation` olarak kalır. Canlı sistemlere ve paylaşılan servislere istek atılmaz.
- **Sonuç:** `confirmed` bulgular önem derecesi ve önerilen düzeltmesiyle kullanıcıya bildirilir; yüksek ve kritik bulgular yayından önce bildirilir.

## Yanıt üslubu (caveman)

Bu depoda `.claude/skills/caveman/` ve `.claude/skills/ultracave/` bulunur (kaynak: github.com/JuliusBrussee/caveman, commit 9b24366; Apache-2.0, LICENSE ve NOTICE klasörlerde).

- **Sohbet yanıtları caveman sesiyle verilir:** önce cevap, sonra neden, sonra sonraki adım. Selamlama, tekrar ve kapanış cümlesi yok. Her teknik bilgi korunur; değil, asla, yalnız gibi sözcükler düşmez; kod, komut, yol, sayı ve hata metni aynen kalır.
- **Dil:** yanıt kullanıcının dilinde verilir (Türkçe).
- **Kalıcı metinler normal yazılır:** kod, yorum, commit mesajı, doküman, sürüm notu, PR ve bilet.
- **Tam cümleye dönülen yerler:** güvenlik uyarısı, geri alınamaz işlem onayı, sırası karışabilecek adımlar, kafası karışan kullanıcı ve kullanıcıya sorulan soru.
- **Kapatmak için:** "stop caveman" ya da "normal mode".

## Çalışma yöntemi (superpowers)

Bu depoda `.claude/skills/` altında obra/superpowers skill'leri bulunur (commit 22f0b46, MIT; LICENSE her skill klasöründe): using-superpowers, brainstorming, writing-plans, executing-plans, test-driven-development, systematic-debugging, verification-before-completion, requesting-code-review, receiving-code-review, using-git-worktrees, finishing-a-development-branch, dispatching-parallel-agents, subagent-driven-development, writing-skills, diagnosing-superpowers.

- Her görevde işe başlamadan uygun skill'i kullan (using-superpowers).
- Yeni özellik: brainstorming, sonra writing-plans, sonra test-driven-development.
- Hata: systematic-debugging; kök neden bulunmadan düzeltme yapılmaz.
- "Bitti" demeden önce verification-before-completion: testi ve çıktıyı kendin gör.
- Öncelik sırası: kullanıcının talimatı, sonra bu CLAUDE.md, sonra skill'ler.

## Arayüz tasarımı (ui-ux-pro-max)

Bu depoda `.claude/skills/ui-ux-pro-max/` bulunur (nextlevelbuilder/ui-ux-pro-max-skill, commit e59ff37, MIT; LICENSE klasörde).

- Arayüz, bileşen, renk, tipografi, erişilebilirlik ve duyarlı düzen işlerinde bu skill'i kullan.
- Kapalı ağda çalışan ürünlerde dış font, CDN ya da harici görsel bağlantısı ekleme; yerel varlık kullan.
