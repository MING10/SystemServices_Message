# SystemServices_Message
调用系统应用API-发送短信/邮件

     #import <MessageUI/MessageUI.h>
     #define NameEmail @"zdm3099@163.com"
     //发送imessage
     - (void)sendMessage{
         //如果能发送文本信息
         if([MFMessageComposeViewController canSendText]){
             MFMessageComposeViewController *messageController=[[MFMessageComposeViewController alloc]init];
             //收件人
             messageController.recipients= [NameEmail componentsSeparatedByString:@","];
             //信息正文
             messageController.body=@"测试发送附件";
             //设置代理,注意这里不是delegate而是messageComposeDelegate
             messageController.messageComposeDelegate=self;
             //如果运行商支持主题
             if([MFMessageComposeViewController canSendSubject]){
                 messageController.subject=@"hello friends";
             }
             //如果运行商支持附件
             if ([MFMessageComposeViewController canSendAttachments]) {
                 /*第一种方法*/
             //            messageController.attachments=...;
            
                 /*第二种方法*/
                 NSArray *attachments= [@"image.png" componentsSeparatedByString:@","];
                 if (attachments.count>0) {
                     [attachments enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
                         NSString *path=[[NSBundle mainBundle]pathForResource:obj ofType:nil];
                         NSURL *url=[NSURL fileURLWithPath:path];
                         [messageController addAttachmentURL:url withAlternateFilename:obj];
                     }]; 
                 }
            
                 /*第三种方法*/
                 //            NSString *path=[[NSBundle mainBundle]pathForResource:@"image.png" ofType:nil];
                 //            NSURL *url=[NSURL fileURLWithPath:path];
                 //            NSData *data=[NSData dataWithContentsOfURL:url];
                 /**
                  *  attatchData:文件数据
                  *  uti:统一类型标识，标识具体文件类型，详情查看：帮助文档中System-Declared Uniform Type Identifiers
                  *  fileName:展现给用户看的文件名称
                  */
                 //            [messageController addAttachmentData:data typeIdentifier:@"public.image"  filename:@"image.png"];
             }
             [self presentViewController:messageController animated:YES completion:nil];
         }
     }
     
     #pragma mark - MFMessageComposeViewController代理方法
     //发送完成，不管成功与否
     -(void)messageComposeViewController:(MFMessageComposeViewController *)controller didFinishWithResult:(MessageComposeResult)result{
         switch (result) {
             case MessageComposeResultSent:
                 NSLog(@"发送成功.");
                 break;
             case MessageComposeResultCancelled:
                 NSLog(@"取消发送.");
                 break;
             default:
                 NSLog(@"发送失败.");
                 break;
         }
         [self dismissViewControllerAnimated:YES completion:nil];
     }
     
     // 发送邮件
     -(void)sendEmail{
         //判断当前是否能够发送邮件
         if ([MFMailComposeViewController canSendMail]) {
             MFMailComposeViewController *mailController=[[MFMailComposeViewController alloc]init];
             //设置代理，注意这里不是delegate，而是mailComposeDelegate
             mailController.mailComposeDelegate=self;
             //设置收件人
             [mailController setToRecipients:[NameEmail componentsSeparatedByString:@","]];
             //设置抄送人
             if (NameEmail.length>0) {
                 [mailController setCcRecipients:[NameEmail componentsSeparatedByString:@","]];
             }
             //设置密送人
             if (NameEmail.length>0) {
                 [mailController setBccRecipients:[NameEmail componentsSeparatedByString:@","]];
             }
             //设置主题
             [mailController setSubject:@"hello friends"];
             //设置内容
             [mailController setMessageBody:@"测试发送邮件" isHTML:YES];
             //添加附件
             if (@"image.png".length>0) {
                 NSArray *attachments=[@"image.png" componentsSeparatedByString:@","] ;
                 [attachments enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
                     NSString *file=[[NSBundle mainBundle] pathForResource:obj ofType:nil];
                     NSData *data=[NSData dataWithContentsOfFile:file];
                     [mailController addAttachmentData:data mimeType:@"image/png" fileName:obj];//第二个参数是mimeType类型，
                /*
                 http://haofeng82.iteye.com/blog/456344
                 超文本标记语言文本 .html,.html text/html
                 普通文本 .txt text/plain
                 RTF文本 .rtf application/rtf
                 GIF图形 .gif image/gif
                 JPEG图形 .ipeg,.jpg image/jpeg
                 au声音文件 .au audio/basic
                 MIDI音乐文件 mid,.midi audio/midi,audio/x-midi
                 RealAudio音乐文件 .ra, .ram audio/x-pn-realaudio
                 MPEG文件 .mpg,.mpeg video/mpeg
                 AVI文件 .avi video/x-msvideo
                 GZIP文件 .gz application/x-gzip
                 TAR文件 .tar application/x-tar
                 */
                 }];
             }
             [self presentViewController:mailController animated:YES completion:nil];
         }
     }
     
     #pragma mark - MFMailComposeViewController代理方法
     -(void)mailComposeController:(MFMailComposeViewController *)controller didFinishWithResult:(MFMailComposeResult)result error:(NSError *)error{
         switch (result) {
             case MFMailComposeResultSent:
                 NSLog(@"发送成功.");
                 break;
             case MFMailComposeResultSaved:
                 //如果存储为草稿（点取消会提示是否存储为草稿，存储后可以到系统邮件应用的对应草稿箱找到）
                 NSLog(@"邮件已保存.");
                 break;
             case MFMailComposeResultCancelled:
                 NSLog(@"取消发送.");
                 break;
                 
                  default:
                 NSLog(@"发送失败.");
                 break;
         }
         if (error) {
             NSLog(@"发送邮件过程中发生错误，错误信息：%@",error.localizedDescription);
         }
         [self dismissViewControllerAnimated:YES completion:nil];
     }
     
     
     
