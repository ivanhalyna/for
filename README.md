<!doctype html>
<html lang="pl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Formularz zamówienia — Hurtownia części komputerowych</title>
  <style>
    /* Minimalny, czytelny styl — bez skomplikowanego designu */
    body{font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial;line-height:1.4;padding:20px;background:#f7f7f7}
    .container{max-width:820px;margin:0 auto;background:#fff;padding:18px;border-radius:8px;box-shadow:0 6px 18px rgba(0,0,0,0.06)}
    h1{font-size:1.4rem;margin:0 0 12px}
    label{display:block;margin-top:12px;font-weight:600}
    .small{font-weight:400;font-size:.92rem;color:#444}
    input[type=text], input[type=email], input[type=tel], input[type=date], select, textarea, input[type=number]{width:100%;padding:8px;border:1px solid #ddd;border-radius:6px;margin-top:6px}
    fieldset{border:1px solid #e6e6e6;padding:12px;border-radius:8px;margin-top:14px}
    legend{font-weight:700}
    .row{display:flex;gap:12px}
    .col{flex:1}
    .inline{display:flex;gap:8px;align-items:center}
    .actions{margin-top:16px;display:flex;gap:8px}
    button{padding:10px 14px;border-radius:8px;border:0;cursor:pointer}
    button[type=submit]{background:#0078d4;color:white}
    button[type=reset]{background:#e6e6e6}
    .hint{font-size:.85rem;color:#666;margin-top:4px}
    .product{display:flex;align-items:center;gap:10px;margin-top:8px}
    .qty{width:100px}
  </style>
</head>
<body>
  <div class="container">
    <h1>Formularz zamówienia — Hurtownia części komputerowych</h1>
    <p class="small">Wypełnij formularz, aby zamówić części do komputerów. Pola oznaczone * są wymagane.</p>

    <form id="orderForm" action="#" method="post" enctype="multipart/form-data" onsubmit="return handleSubmit(event)">
      <label for="company">Nazwa firmy *</label>
      <input id="company" name="company" type="text" required placeholder="Np. ABC Komputery Sp. z o.o.">

      <div class="row">
        <div class="col">
          <label for="contact">Osoba kontaktowa *</label>
          <input id="contact" name="contact" type="text" required placeholder="Imię i nazwisko">
        </div>
        <div class="col">
          <label for="vat">NIP (VAT)</label>
          <input id="vat" name="vat" type="text" placeholder="PL1234567890" pattern="^PL?\d{10}$" title="Wprowadź NIP w formacie PLXXXXXXXXXX lub XXXXXXXXXX">
          <div class="hint">Opcjonalnie — przyspiesza wystawienie faktury.</div>
        </div>
      </div>

      <div class="row">
        <div class="col">
          <label for="email">E-mail *</label>
          <input id="email" name="email" type="email" required placeholder="kontakt@firma.pl">
        </div>
        <div class="col">
          <label for="phone">Telefon *</label>
          <input id="phone" name="phone" type="tel" required placeholder="+48 600 000 000">
        </div>
      </div>

      <fieldset>
        <legend>Wybierz produkty</legend>
        <div class="product">
          <label class="inline"><input type="checkbox" name="product" value="Procesory" data-target="qty-cpu"> Procesory (CPU)</label>
          <input class="qty" id="qty-cpu" name="qty_CPU" type="number" min="1" placeholder="Ilość" disabled>
        </div>
        <div class="product">
          <label class="inline"><input type="checkbox" name="product" value="Karty graficzne" data-target="qty-gpu"> Karty graficzne (GPU)</label>
          <input class="qty" id="qty-gpu" name="qty_GPU" type="number" min="1" placeholder="Ilość" disabled>
        </div>
        <div class="product">
          <label class="inline"><input type="checkbox" name="product" value="Plyty glówne" data-target="qty-mobo"> Płyty główne</label>
          <input class="qty" id="qty-mobo" name="qty_MOBO" type="number" min="1" placeholder="Ilość" disabled>
        </div>
        <div class="product">
          <label class="inline"><input type="checkbox" name="product" value="Dyski SSD" data-target="qty-ssd"> Dyski SSD</label>
          <input class="qty" id="qty-ssd" name="qty_SSD" type="number" min="1" placeholder="Ilość" disabled>
        </div>
        <div class="product">
          <label class="inline"><input type="checkbox" name="product" value="Pamięć RAM" data-target="qty-ram"> Pamięć RAM</label>
          <input class="qty" id="qty-ram" name="qty_RAM" type="number" min="1" placeholder="Ilość" disabled>
        </div>
        <div class="hint">Jeśli chcesz zamówić inny przedmiot, opisz go w polu <strong>Uwagi</strong>.</div>
      </fieldset>

      <label for="delivery">Preferowana data dostawy</label>
      <input id="delivery" name="delivery" type="date">

      <label for="payment">Metoda płatności</label>
      <select id="payment" name="payment">
        <option value="przelew">Przelew bankowy</option>
        <option value="platnosc_przy_odbiorze">Płatność przy odbiorze</option>
        <option value="karta">Karta płatnicza</option>
      </select>

      <label for="file">Załącz dokument (np. zamówienie w .pdf) — opcjonalnie</label>
      <input id="file" name="file" type="file" accept=".pdf,.doc,.docx">

      <label for="notes">Uwagi / specyfikacja dodatkowa</label>
      <textarea id="notes" name="notes" rows="5" placeholder="Np. konkretne modele, budżet, wymagania gwarancyjne"></textarea>

      <div class="actions">
        <button type="submit">Wyślij zamówienie</button>
        <button type="reset">Wyczyść formularz</button>
      </div>

      <p id="result" class="hint" aria-live="polite" style="margin-top:10px"></p>
    </form>
  </div>

  <script>
    // Włączanie / wyłączanie pola ilości kiedy checkbox jest zaznaczony
    document.querySelectorAll('input[type=checkbox][name="product"]').forEach(cb=>{
      cb.addEventListener('change', e=>{
        const targetId = e.target.dataset.target;
        const qty = document.getElementById(targetId);
        if(!qty) return;
        qty.disabled = !e.target.checked;
        if(!e.target.checked) qty.value = '';
      });
    });

    function handleSubmit(e){
      e.preventDefault();
      const form = e.target;
      // Prosta walidacja: co najmniej jeden produkt z ilością
      const products = Array.from(form.querySelectorAll('input[type=checkbox][name="product"]'))
        .filter(ch => ch.checked);
      if(products.length===0){
        alert('Wybierz przynajmniej jeden produkt i podaj ilość.');
        return false;
      }
      for(const p of products){
        const qty = document.getElementById(p.dataset.target);
        if(!qty || !qty.value || Number(qty.value) < 1){
          alert('Dla każdego wybranego produktu podaj ilość (>=1).');
          return false;
        }
      }

      // Zbierz dane do szybkiego podsumowania (lokalnie — bez wysyłki):
      const data = new FormData(form);
      const summary = {
        company: data.get('company'),
        contact: data.get('contact'),
        email: data.get('email'),
        phone: data.get('phone'),
        products: products.map(p => ({name: p.value, qty: data.get(p.dataset.target.replace('qty-','qty_') )}))
      };

      // Pokaż przyjazne potwierdzenie (tu normalnie wysłalibyśmy formularz na serwer)
      const result = document.getElementById('result');
      result.textContent = 'Dziękujemy — zamówienie przygotowane lokalnie. Sprawdź dane i wyślij przez system back-office.';

      console.log('Podsumowanie zamówienia (przykład):', summary);
      return false; // zatrzymujemy rzeczywiste wysłanie (brak backendu)
    }
  </script>
</body>
</html>
