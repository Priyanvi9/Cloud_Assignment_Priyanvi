# Cloud_Assignment_Priyanvi
# Swachh Sahayak Pledge Application ✨

## A Serverless Static Web Application for Cleanliness Pledges with Automated Image Processing

---

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Static Website](https://img.shields.io/badge/Website-Static-green)](https://[YOUR_CLOUDFRONT_DOMAIN_OR_S3_WEBSITE_URL])
[![Serverless](https://img.shields.io/badge/Architecture-Serverless-orange)](https://aws.amazon.com/serverless/)
[![Built with AWS](https://img.shields.io/badge/Cloud-AWS-FF9900.svg?logo=amazon-aws)](https://aws.amazon.com/)

---

## 🚀 Project Overview

The **Swachh Sahayak Pledge Application** is a cloud-native, static web application designed to encourage and capture pledges for cleanliness and community support (`Swachh Sahayak` roughly translates to "Cleanliness Helper/Supporter"). This project was developed as a part of a Cloud Computing assignment, showcasing proficiency in building scalable, cost-effective, and robust web solutions using Amazon Web Services (AWS).

A unique feature of this application is its **serverless image processing pipeline**, which automatically handles user-uploaded pledge images.

## ✨ Features

* **Pledge Submission:** Users can easily submit their personal pledges for cleanliness and community well-being.
* **Image Upload:** Users can upload a relevant image along with their pledge, such as a photo of their clean efforts, a drawing, or a symbolic image.
* **Automated Image Processing:**
    * Uploaded images are automatically processed (e.g., resized, optimized, watermarked) using AWS Lambda.
    * This ensures images are web-friendly, consistent, and reduce storage/delivery costs.
* **Static Website Hosting:** The application frontend is hosted on Amazon S3, providing high availability, low latency, and enhanced security.
* **Content Delivery Network (CDN):** Leverages Amazon CloudFront for global content delivery, ensuring fast load times for users worldwide.
* **Scalable & Cost-Effective:** Built entirely on serverless and managed AWS services, making it inherently scalable to handle varying loads without managing servers, and highly cost-efficient (pay-per-use model).
* **User-Friendly Interface:** A simple and intuitive interface for making pledges.

## 📐 Architecture

The application is built on a robust serverless architecture on AWS, designed for efficiency and scalability:

1.  **Frontend (Static Website):**
    * Hosted on **Amazon S3** configured for static website hosting.
    * Distributed globally via **Amazon CloudFront** for fast content delivery.
    * Built using HTML, CSS, and JavaScript.

2.  **Image Upload & Processing:**
    * When a user uploads an image from the frontend, it is directly uploaded to a designated **Amazon S3 Bucket (Original Images)**. This is typically achieved using pre-signed URLs or direct PUT requests from the browser's JavaScript.
    * An **S3 Event Notification** is configured on the "Original Images" bucket to trigger an AWS Lambda function whenever a new image is uploaded.
    * **AWS Lambda (Image Processor):** This serverless function executes Python (or Node.js/other runtime) code to perform image manipulation (e.g., resizing to standard dimensions, optimizing for web, adding a watermark/overlay).
    * The processed image is then saved to a separate **Amazon S3 Bucket (Processed Images)**.
    * (Optional: If pledge *text* is also submitted via an API, **Amazon API Gateway** would trigger another Lambda function to store text data, perhaps in DynamoDB).

---

```mermaid
graph TD
    A[User Browser] -->|1. Request Website Content| B(CloudFront CDN)
    B -->|2. Serve Static Assets| C(Amazon S3 - Website Bucket)

    A -->|3. Upload Image (via JS)| D(Amazon S3 - Original Images Bucket)
    D -->|4. New Object Created Trigger| E(AWS Lambda - Image Processor)
    E -->|5. Process Image & Save| F(Amazon S3 - Processed Images Bucket)

    F -->|6. Display Processed Images| 
