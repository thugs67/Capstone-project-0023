import os
from pydantic import BaseModel
from typing import List

class CreditDecision(BaseModel): user_id: str; score: int; reason: str

# Simulated bias monitoring
DISPARATE_IMPACT_THRESHOLD = 0.8 

def run_ethics_gate(raw_data: dict, demographic: str) -> dict:
    # 1. OASIS: Opt-In & Intentionality Check (Kenya DPA Sec 25 & 30)
    if not raw_data.get("explicit_consent") or raw_data.get("purpose") != "credit_scoring":
        return {"status": "BLOCKED", "reason": "Violates Kenya DPA: Consent/Purpose Limitation"}
    
    # 2. OASIS: Anonymization & Sovereignty (Local processing assumed)
    sanitized_data = {k: v for k, v in raw_data.items() if k not in ["phone_number", "exact_location"]}
    
    # 3. TRACK: Counterfactual & Representation Check (Simulated)
    disparate_impact = calculate_disparate_impact(demographic, sanitized_data) # Placeholder func
    
    # 4. TRACK: Kill Switch
    if disparate_impact < DISPARATE_IMPACT_THRESHOLD:
        return {"status": "KILL_SWITCH_TRIGGERED", "reason": "Bias threshold breached. Model halted."}
    
    return {"status": "APPROVED", "data": sanitized_data}

# Usage: run_ethics_gate({"explicit_consent": True, "purpose": "credit_scoring", "phone_number": "07XX", "exact_location": "Kibera"}, "Female_Rural")
