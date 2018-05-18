---
layout: post
title: DealGlobe rails notes
---
请不要提交本notes

发邮件

CmsMailer.recommended_mail(u, [], [], Platform::Procedure.limit(1).all).deliver_now


邮件预览

http://127.0.0.1:3000/rails/mailers/cms_mailer/recommended_mail
test/mailers/previews/cms_mailer_preview.rb里面添加method

Platform::Procedure.mergerintel.live_deals #原始的live deals信息
