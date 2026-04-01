# Security Policy

## Scope

ECT is a theoretical research framework. It does not ship executable software in its current form.

However, as ECT evolves into implementations (ECT type system in SLIME, ECT-AI reasoning engine), security considerations will apply to those components.

## Reporting

If you discover a vulnerability in any implementation component of this repository:

1. **Do not open a public issue.**
2. Email directly: **divinesunday247@gmail.com**
3. Include:
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)

You will receive a response within 72 hours.

## Theoretical Security Considerations

ECT-3 (Observer Epistemic Relativity) has direct implications for AI system security:

> If an AI system's epistemic boundary is observer-relative, then adversarial observers can exploit boundary mismatches to extract information the system believes it cannot reveal.

This is an active area of research within the ECT program. See `docs/ect-3-formal.md` for details.

## Supported Versions

| Version | Status |
|---------|--------|
| 0.1.x | Active research — no executable components |
| Future | Security policy will expand with implementation |
