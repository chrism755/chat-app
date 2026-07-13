export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  const { to, code } = req.body || {};
  if (!to || !code) {
    return res.status(400).json({ error: 'Missing "to" or "code"' });
  }

  try {
    const response = await fetch('https://api.resend.com/emails', {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${process.env.RESEND_API_KEY}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        // TESTING: using Resend's shared sender since netlinkagencies.linkpc.net
        // isn't verified yet. Swap back to the noreply@netlinkagencies... address
        // once that domain shows "Verified" in the Resend dashboard.
        from: 'Meridian Chat <onboarding@resend.dev>',
        to: [to],
        subject: 'Your Meridian Chat verification code',
        html: `<p>Your verification code is:</p><h2 style="letter-spacing:4px;">${code}</h2><p>If you didn't request this, you can ignore this email.</p>`
      })
    });

    const data = await response.json();
    if (!response.ok) {
      console.error('Resend error:', data);
      return res.status(response.status).json({ error: data.message || 'Failed to send email' });
    }

    return res.status(200).json({ success: true });
  } catch (err) {
    console.error('send-code error:', err);
    return res.status(500).json({ error: 'Server error sending email' });
  }
}
