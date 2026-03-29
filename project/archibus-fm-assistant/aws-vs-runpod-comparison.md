---
publish: true
date: 2025-12-01
---

# AWS vs RunPod GPU Comparison
[[client-archibus]]

## 1. Executive Summary

- AWS and RunPod offer different GPU compute models with distinct trade-offs in single-GPU availability and pricing structure
- Key differentiator: RunPod offers single-GPU instances for high-end GPUs (A100, H100, H200, B200) where AWS requires 8x bundles
- Data compiled from official AWS documentation and RunPod pricing pages (December 2025)
- **Note:** For cold start mechanics, see `serverless-llm-cold-start-mechanics.md` (global)

## 2. Product Category Mapping

- **RunPod Serverless** ↔ **SageMaker Real-Time Inference**: Managed services with scale-to-zero capability
- **RunPod Pod** ↔ **EC2 GPU Instances**: Always-on hourly billing with full infrastructure control
- Eliminated from comparison: SageMaker Serverless (CPU-only), SageMaker Async (not real-time), Inferentia2 (custom silicon complexity)

## 3. AWS Products Eliminated from Comparison

| Product | Reason for Elimination | Source |
|---------|------------------------|--------|
| **SageMaker Serverless** | No GPU support - architecture based on AWS Lambda which lacks GPU capability | [AWS Docs](https://docs.aws.amazon.com/sagemaker/latest/dg/serverless-endpoints.html) |
| **SageMaker Async** | Queued/batch processing model - requests placed in queue, not suitable for real-time chatbot responses | [AWS Docs](https://docs.aws.amazon.com/sagemaker/latest/dg/async-inference.html) |
| **Inferentia2** | Custom NVIDIA-alternative silicon requiring vLLM source builds, smaller ecosystem, adds deployment complexity | [AWS Neuron SDK](https://awsdocs-neuron.readthedocs-hosted.com/) |

## 4. Single-GPU Availability

| GPU | RunPod | SageMaker Inference | EC2 | Source |
|-----|--------|---------------------|-----|--------|
| A10G (24GB) | ✅ Single | ✅ ml.g5.xlarge | ✅ g5.xlarge | [AWS Pricing](https://aws.amazon.com/sagemaker/pricing/) |
| L40S (48GB) | ✅ Single | ✅ ml.g6e.xlarge | ✅ g6e.xlarge | [AWS Announcement](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-sagemaker-ai-p5e-g6e-instances-inference/) |
| A100 (80GB) | ✅ Single | ❌ 8x only (ml.p4d) | ❌ 8x only (p4d) | [AWS Docs](https://aws.amazon.com/ec2/instance-types/p4/) |
| H100 (80GB) | ✅ Single | ❌ 8x only (ml.p5) | ✅ p5.4xlarge (Aug 2025) | [AWS Announcement](https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-p5-single-gpu-instances-now-available/) |
| H200 (141GB) | ✅ Single | ❌ 8x only (ml.p5e) | ❌ 8x only (p5e) | [AWS Docs](https://aws.amazon.com/ec2/instance-types/p5/) |
| B200 (180GB) | ✅ Single | ❌ Not available | ❌ Not available | [RunPod Pricing](https://www.runpod.io/pricing) |

## 5. Serverless Pricing Comparison (Scale-to-Zero)

| GPU | Memory | RunPod Serverless | SageMaker Real-Time | Delta | Source |
|-----|--------|-------------------|---------------------|-------|--------|
| L40S | 48GB | $1.90/hr | $2.61/hr (ml.g6e.xlarge) | +37% AWS | [RunPod](https://www.runpod.io/pricing), [CloudPrice](https://cloudprice.net/aws/sagemaker/) |
| A10G | 24GB | ~$1.10/hr | $2.03/hr (ml.g5.xlarge) | +85% AWS | [RunPod](https://www.runpod.io/pricing), [AWS](https://aws.amazon.com/sagemaker/pricing/) |
| A100 | 80GB | $2.72/hr | $37.69/hr (8x only) | N/A - no single GPU | [RunPod](https://www.runpod.io/pricing), [AWS](https://aws.amazon.com/sagemaker/pricing/) |
| H100 | 80GB | $4.18/hr | ~$113/hr (8x only) | N/A - no single GPU | [RunPod](https://www.runpod.io/pricing), [AWS](https://aws.amazon.com/sagemaker/pricing/) |
| H200 | 141GB | $5.58/hr | TBD (8x only) | N/A - no single GPU | [RunPod](https://www.runpod.io/pricing) |
| B200 | 180GB | $8.64/hr | Not available | N/A | [RunPod](https://www.runpod.io/pricing) |

## 6. Always-On Pricing Comparison (Pod vs EC2)

| GPU | Memory | RunPod Pod | EC2 On-Demand | EC2 Spot | Source |
|-----|--------|------------|---------------|----------|--------|
| A10G | 24GB | ~$0.85/hr | $1.01/hr (g5.xlarge) | ~$0.30-0.50/hr | [RunPod](https://www.runpod.io/pricing), [AWS](https://aws.amazon.com/ec2/pricing/) |
| L40S | 48GB | ~$1.33/hr | TBD (g6e.xlarge) | TBD | [RunPod](https://www.runpod.io/pricing) |
| A100 | 80GB | ~$2.17/hr | $32.77/hr (8x p4d) | N/A | [RunPod](https://www.runpod.io/pricing), [AWS](https://aws.amazon.com/ec2/pricing/) |
| H100 | 80GB | ~$3.35/hr | $55.04/hr (8x p5) | $27.97/hr (8x) | [RunPod](https://www.runpod.io/pricing), [Vantage](https://instances.vantage.sh/) |
| H200 | 141GB | ~$4.46/hr | $63.30/hr (8x p5en) | $17.27/hr (8x) | [RunPod](https://www.runpod.io/pricing), [Vantage](https://instances.vantage.sh/) |

## 7. GPU Generation Availability Summary

| Generation | RunPod Serverless | SageMaker Inference | EC2 |
|------------|-------------------|---------------------|-----|
| **B200** (Blackwell, 180GB) | ✅ $8.64/hr | ❌ Not available | ❌ Not available |
| **H200** (Hopper, 141GB) | ✅ $5.58/hr | ⚠️ 8x only | ⚠️ 8x only |
| **H100** (Hopper, 80GB) | ✅ $4.18/hr | ⚠️ 8x only | ✅ Single (Aug 2025) |
| **A100** (Ampere, 80GB) | ✅ $2.72/hr | ⚠️ 8x only | ⚠️ 8x only |
| **L40S** (Ada, 48GB) | ✅ $1.90/hr | ✅ Single | ✅ Single |
| **A10G** (Ampere, 24GB) | ✅ ~$1.10/hr | ✅ Single | ✅ Single |

## 8. Commitment-Based Pricing

| Provider | Term | Discount | Source |
|----------|------|----------|--------|
| **RunPod** | 3 months | ~15% | [RunPod Savings Plan](https://docs.runpod.io/pods/savings-plans) |
| **RunPod** | 6 months | ~20-25% | [RunPod Savings Plan](https://docs.runpod.io/pods/savings-plans) |
| **AWS** | 1 year | ~30% | [AWS Savings Plans](https://aws.amazon.com/savingsplans/) |
| **AWS** | 3 years | ~50% | [AWS Savings Plans](https://aws.amazon.com/savingsplans/) |

**Key Differences:**
- RunPod maximum term: 6 months ("we currently do not offer the option to commit for one year up-front")
- AWS maximum term: 3 years with deeper discounts
- Neither commitment changes cold start behavior or GPU availability

## 9. Source References

### AWS Official Documentation
- [SageMaker Pricing](https://aws.amazon.com/sagemaker/pricing/)
- [SageMaker Scale-to-Zero Blog](https://aws.amazon.com/blogs/machine-learning/unlock-cost-savings-with-the-new-scale-down-to-zero-feature-in-amazon-sagemaker-inference/)
- [SageMaker Serverless Endpoints](https://docs.aws.amazon.com/sagemaker/latest/dg/serverless-endpoints.html)
- [SageMaker Scale to Zero Instances](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling-zero-instances.html)
- [EC2 P4d Instances](https://aws.amazon.com/ec2/instance-types/p4/)
- [EC2 P5 Instances](https://aws.amazon.com/ec2/instance-types/p5/)
- [Single GPU P5 Instances Announcement](https://aws.amazon.com/about-aws/whats-new/2025/08/amazon-p5-single-gpu-instances-now-available/)
- [P5e/G6e Instances for Inference](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-sagemaker-ai-p5e-g6e-instances-inference/)

### AWS Community
- [AWS re:Post - SageMaker Serverless GPU Question](https://repost.aws/questions/QUdIP4nsQeRF6JA1e6aw0W9g/how-can-i-run-sagemaker-serverless-inference-on-a-gpu-instance)
- [AWS re:Post - GPU Serverless for LLMs](https://repost.aws/questions/QUlHAbaJiIRt-eem9gizSmOQ/is-gpu-serverless-inferencing-for-custom-llm-models)

### RunPod Documentation
- [RunPod Pricing](https://www.runpod.io/pricing)
- [RunPod FlashBoot Blog](https://www.runpod.io/blog/introducing-flashboot-serverless-cold-start)
- [RunPod Serverless Overview](https://docs.runpod.io/serverless/overview)
- [RunPod Endpoint Configurations](https://docs.runpod.io/serverless/references/endpoint-configurations)
- [RunPod Savings Plans](https://docs.runpod.io/pods/savings-plans)

### AWS Savings Plans
- [AWS Savings Plans](https://aws.amazon.com/savingsplans/)

### Third-Party Pricing Aggregators
- [CloudPrice - SageMaker Instances](https://cloudprice.net/aws/sagemaker/)
- [Vantage - EC2 Instance Pricing](https://instances.vantage.sh/)

---

*Report generated: 2025-12-01*
*Related Issue: DaveX2001/deliverable-tracking#9*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

